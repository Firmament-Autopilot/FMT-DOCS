## API
API Name                                            | API Usage                  | Input        
-----                                               | --------------             | --------------
start()                                             | 开始执行Offboard指令         | None
stop()                                              | 停止执行Offboard指令         | None
set_velocity_ned(VelocityNedYaw velocity_ned_yaw)   | 设置NED坐标系中的速度和航向角  | 包含物体在NED坐标系中各方向的速度分量（北、东、下）及航向角（Yaw）的结构体
set_acceleration_ned(AccelerationNed acceleration_ned) | 设置NED坐标系中的加速度      | 包含物体在NED坐标系中各方向的加速度分量（北、东、下）的结构体
set_position_ned(PositionNedYaw position_ned_yaw)   | 设置NED坐标系中的位置和航向角  | 包含物体在NED坐标系中的位置（北、东、下）及航向角（Yaw）的结构体
set_position_global(PositionGlobalYaw position_global_yaw) | 设置全球坐标系中的位置和航向角 | 包含物体在全球坐标系中的位置（经度、纬度、高度）及航向角（Yaw）的结构体
set_attitude(Attitude attitude)                     | 设置姿态                    | 包含物体的姿态（滚转、俯仰、偏航）的结构体
set_attitude_rate(AttitudeRate attitude_rate)       | 设置姿态速率                | 包含物体的姿态速率（滚转、俯仰、偏航速率）的结构体
set_velocity_body(VelocityBodyYawspeed velocity_body_yawspeed) | 设置机体坐标系中的速度和偏航速率 | 包含物体在机体坐标系中各方向的速度分量（前、右、下）及偏航速率的结构体

## 测试程序

以下是一份用于测试 Offboard 插件 API 的示例程序，演示了如何通过 MAVSDK 使用 Offboard 控制飞行器的运动和姿态。代码中顺序执行了连接到飞行器，起飞，启动 Offboard 控制，设置NED系下飞行器的速度、加速度、位置、Global位置、姿态角、姿态角速度、机体系下的速度，并在完成所有Offboard指令后着陆。
```
#include <chrono>
#include <cmath>
#include <future>
#include <iostream>
#include <thread>

#include <mavsdk/mavsdk.h>
#include <mavsdk/plugins/action/action.h>
#include <mavsdk/plugins/offboard/offboard.h>
#include <mavsdk/plugins/telemetry/telemetry.h>

using namespace mavsdk;
using std::chrono::milliseconds;
using std::chrono::seconds;
using std::this_thread::sleep_for;

// 打印程序使用说明
void usage(const std::string& bin_name)
{
    std::cerr << "Usage : " << bin_name << " <connection_url>\n"
              << "Connection URL format should be :\n"
              << " For TCP : tcp://[server_host][:server_port]\n"
              << " For UDP : udp://[bind_host][:bind_port]\n"
              << " For Serial : serial:///path/to/serial/dev[:baudrate]\n"
              << "For example, to connect to the simulator use URL: udpin://0.0.0.0:14540\n";
}

int main(int argc, char** argv)
{
    // 检查输入的参数是否正确
    if (argc != 2) {
        usage(argv[0]);
        return 1;
    }

    // 创建Mavsdk对象，指定组件类型为地面站（GroundStation）
    Mavsdk mavsdk{Mavsdk::Configuration{Mavsdk::ComponentType::CompanionComputer}};
    ConnectionResult connection_result = mavsdk.add_any_connection(argv[1]); // 添加连接

    // 如果连接失败，打印错误并退出
    if (connection_result != ConnectionResult::Success) {
        std::cerr << "Connection failed: " << connection_result << '\n';
        return 1;
    }

    // 获取连接的系统（自动驾驶系统），如果超时，则退出
    auto system = mavsdk.first_autopilot(3.0);
    if (!system) {
        std::cerr << "Timed out waiting for system\n";
        return 1;
    }

    // 实例化插件对象
    auto action = Action{system.value()};        // 动作插件
    auto offboard = Offboard{system.value()};    // Offboard插件
    auto telemetry = Telemetry{system.value()};  // 遥测插件

    // 等待系统健康状态检查通过
    while (!telemetry.health_all_ok()) {
        std::cout << "Waiting for system to be ready\n";
        sleep_for(seconds(1));
    }
    std::cout << "System is ready\n";

    // 设置位置信息遥测数据更新频率为1Hz
    const auto set_position_global_rate_result = telemetry.set_rate_position(1.0);
    if (set_position_global_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting position rate failed: " << set_position_global_rate_result << '\n';
        return 1;
    }

    // 解锁（arming）操作
    const auto arm_result = action.arm();
    if (arm_result != Action::Result::Success) {
        std::cerr << "Arming failed: " << arm_result << '\n';
        return 1;
    }
    std::cout << "Armed\n";

    // 设置起飞高度为2米
    const auto set_takeoff_height_result = action.set_takeoff_altitude(2.0f);
    if (set_takeoff_height_result != Action::Result::Success) {
        std::cerr << "Setting takeoff altitude failed: " << set_takeoff_height_result << '\n';
        return 1;
    }

    // 起飞
    const auto takeoff_result = action.takeoff();
    if (takeoff_result != Action::Result::Success) {
        std::cerr << "Takeoff failed: " << takeoff_result << '\n';
        return 1;
    }

    // 使用 promise 和 future 来等待起飞完成
    auto in_air_promise = std::promise<void>{};
    auto in_air_future = in_air_promise.get_future();
    Telemetry::LandedStateHandle handle = telemetry.subscribe_landed_state(
        [&telemetry, &in_air_promise, &handle](Telemetry::LandedState state) {
            if (state == Telemetry::LandedState::InAir) {
                std::cout << "Taking off has finished\n.";
                telemetry.unsubscribe_landed_state(handle);
                in_air_promise.set_value();
            }
        });

    // 等待最多10秒，如果起飞超时则报告错误
    in_air_future.wait_for(seconds(10));
    if (in_air_future.wait_for(seconds(15)) == std::future_status::timeout) {
        std::cerr << "Takeoff timed out.\n";
        return 1;
    }
    
    // 在开始Offboard控制之前必须先给出一条Offboard指令，否则会启动失败
    const Offboard::VelocityNedYaw stay{};  // 空的VelocityNedYaw指令
    offboard.set_velocity_ned(stay);

    // 启动Offboard控制
    Offboard::Result offboard_result = offboard.start();
    if (offboard_result != Offboard::Result::Success) {
        std::cerr << "Offboard start failed: " << offboard_result << '\n';
        return 1;
    }

    std::cout << "velocity ned\n";
    // 设置NED坐标系下的速度
    Offboard::VelocityNedYaw vel_ned{};
    vel_ned.yaw_deg = 270.0f;   // 设置航向角为270度
    vel_ned.down_m_s = -1.0f;   // 设置向下速度为-1 m/s
    vel_ned.east_m_s = -1.0f;   // 设置向东速度为-1 m/s
    vel_ned.north_m_s = 1.0f;   // 设置向北速度为1 m/s
    offboard.set_velocity_ned(vel_ned);  // 设置速度
    std::cout << vel_ned << '\n';
    sleep_for(seconds(5));

    std::cout << "accleration ned\n";
    // 设置NED坐标系下的加速度
    Offboard::AccelerationNed acc_ned{};
    acc_ned.down_m_s2 = -1.0f;  // 设置向下加速度为-1 m/s^2
    acc_ned.east_m_s2 = -1.0f;  // 设置向东加速度为-1 m/s^2
    acc_ned.north_m_s2 = 1.0f;  // 设置向北加速度为1 m/s^2
    offboard.set_acceleration_ned(acc_ned);  // 设置加速度
    std::cout << acc_ned << '\n';
    sleep_for(seconds(5));

    std::cout << "position ned\n";
    // 设置NED坐标系下的期望位置
    Offboard::PositionNedYaw pos_ned{};
    pos_ned.yaw_deg = 0.0f;   // 设置航向角为0度
    pos_ned.down_m = -15.0f;  // 设置下向位置为-15米
    pos_ned.east_m = -10.0f;  // 设置东向位置为-10米
    pos_ned.north_m = 10.0f;  // 设置北向位置为10米
    offboard.set_position_ned(pos_ned);  // 设置位置
    std::cout << pos_ned << '\n';
    sleep_for(seconds(15));

    std::cout << "get position global\n";
    // 获取global位置
    auto position_global = telemetry.position();
    std::cout << "latitude: " << position_global.latitude_deg << '\n';
    std::cout << "longitude: " << position_global.longitude_deg << '\n';
    std::cout << "absolute altitude: " << position_global.absolute_altitude_m << '\n';
    std::cout << "relative altitude: " << position_global.relative_altitude_m << '\n';

    std::cout << "set a target position global\n";
    // 设置目标global位置
    Offboard::PositionGlobalYaw pos_global{};
    pos_global.alt_m = 20.0f;      // 设置绝对高度为20米
    pos_global.lat_deg = position_global.latitude_deg + 0.0001f; // 纬度
    pos_global.lon_deg = position_global.longitude_deg + 0.0001f; // 经度
    pos_global.yaw_deg = 90.0f;   // 设置目标航向角为90度
    std::cout << "position global\n";
    offboard.set_position_global(pos_global);  // 设置目标位置
    std::cout << pos_global << '\n';
    sleep_for(seconds(15));

    std::cout << "attitude\n";
    // 设置姿态
    Offboard::Attitude att{};
    att.pitch_deg = 10.0f;      // 设置俯仰角为10度
    att.roll_deg = 10.0f;       // 设置滚转角为10度
    att.yaw_deg = 0.0f;         // 设置航向角为0度
    att.thrust_value = 0.48f;   // 设置油门值
    offboard.set_attitude(att); // 设置姿态
    std::cout << att << '\n';
    sleep_for(seconds(10));

    std::cout << "attiude rate\n";
    // 设置姿态变化速率
    Offboard::AttitudeRate att_rate{};
    att_rate.pitch_deg_s = 1.0f;    // 设置俯仰角速率
    att_rate.roll_deg_s = 1.0f;     // 设置滚转角速率
    att_rate.yaw_deg_s = 1.0f;      // 设置航向角速率
    att_rate.thrust_value = 0.465f; // 设置油门值
    offboard.set_attitude_rate(att_rate); // 设置姿态速率
    std::cout << att_rate << '\n';
    sleep_for(seconds(10));

    std::cout << "velocity body\n";
    // 设置机体坐标系下的速度
    Offboard::VelocityBodyYawspeed vel_body{};
    vel_body.down_m_s = -1.0f;     // 设置向下速度为-1 m/s
    vel_body.forward_m_s = 1.0f;   // 设置向前速度为1 m/s
    vel_body.right_m_s = 1.0f;     // 设置向右速度为1 m/s
    vel_body.yawspeed_deg_s = 3.0f; // 设置航向角速率为3度/秒
    offboard.set_velocity_body(vel_body); // 设置机体速度
    std::cout << vel_body << '\n';
    sleep_for(seconds(120));

    // 着陆操作
    const auto land_result = action.land();
    if (land_result != Action::Result::Success) {
        std::cerr << "Landing failed: " << land_result << '\n';
        return 1;
    }

    // 检查飞行器是否仍在空中
    while (telemetry.in_air()) {
        std::cout << "Vehicle is landing...\n";
        sleep_for(seconds(1));
    }
    std::cout << "Landed!\n";

    // 等待飞行器自动解除锁定\
    sleep_for(seconds(3));
    std::cout << "Finished...\n";

    return 0;
}
```