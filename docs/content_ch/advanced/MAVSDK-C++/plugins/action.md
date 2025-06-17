## API
API Name    | API Usage             | Input        
-----       | --------------        | -------------- 
Arm()       | send arm command      | None     
Disarm()    | send disarm command   | None       
takeoff()   | send takeoff command  | None 
land()      | send land command     | None 
reboot()    | send reboot command   | None
hold()      | send hold command     | None 
return_to_launch()      | send return to launch point command   | None
go_to_location(double latitude_deg, double longitude_deg, float absolute_altitude_m, float yaw_deg)        | send go to given location command     | 期望达到的经度,纬度,高度以及运行过程中的偏航角度
set_takeoff_altitude(float altitude)  | set the takeoff altitude              | 期望的起飞高度
set_current_speed(float speed_m_s)     | set flight speed                      | 期望的巡航速度

> 根据FMT对```go_to_location(double latitude_deg, double longitude_deg, float absolute_altitude_m, float yaw_deg)```函数的处理，其中的参数```yaw_deg```在移动到航点的过程中不会作为控制量用于控制被控对象在移动到指定的位置过程中的偏航。

## 测试程序

下面是一份用于测试action插件API的示例程序。程序发出的命令顺序为解锁、起飞、移动到指定位置、悬停在指定位置、返回起飞点、降落、上锁最后重启。

```
//在头文件中引入Action与Telemetry插件库

#include <chrono>  
#include <cstdint> 
#include <mavsdk/mavsdk.h> 
#include <mavsdk/plugins/action/action.h> // 引入 MAVSDK 的 Action 插件库，用于执行无人机的动作（如起飞、着陆、悬停等）
#include <mavsdk/plugins/telemetry/telemetry.h> // 引入 MAVSDK 的 Telemetry 插件库，用于获取无人机的遥测数据（如位置、姿态、速度等）
#include <iostream>  
#include <future>    
#include <memory>    
#include <thread>    
#include <iomanip>   

using namespace mavsdk;
using std::chrono::seconds;
using std::this_thread::sleep_for;

void usage(const std::string& bin_name)
{
    std::cerr << "Usage : " << bin_name << " <connection_url>\n"
              << "Connection URL format should be :\n"
              << " For TCP : tcp://[server_host][:server_port]\n"
              << " For UDP : udp://[bind_host][:bind_port]\n"
              << " For Serial : serial:///path/to/serial/dev[:baudrate]\n"
              << "For example, to connect to the simulator use URL: udp://:14540\n";
}

int main(int argc, char** argv)
{
    if (argc != 2) {
        usage(argv[0]);
        return 1;
    }

    Mavsdk mavsdk{Mavsdk::Configuration{Mavsdk::ComponentType::CompanionComputer}};
    ConnectionResult connection_result = mavsdk.add_any_connection(argv[1]);

    if (connection_result != ConnectionResult::Success) {
        std::cerr << "Connection failed: " << connection_result << '\n';
        return 1;
    }

    
    // 获取第一个 autopilot 系统
    auto system = mavsdk.first_autopilot(3.0);
    if (!system) {
        std::cerr << "Timed out waiting for system\n";
        return 1;
    }

    // 实例化插件：telemetry（遥测）和 action（动作）
    auto telemetry = Telemetry{system.value()};
    auto action = Action{system.value()};

    // 设置遥测数据更新频率为 1 Hz
    const auto set_pos_rate_result = telemetry.set_rate_position(1.0);
    if (set_pos_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting position rate failed: " << set_pos_rate_result << '\n';
        return 1;
    }
    
    // 设置姿态欧拉角数据更新频率为 1 Hz
    const auto set_angle_euler_result = telemetry.set_rate_attitude_euler(1.0);
    if (set_angle_euler_result != Telemetry::Result::Success) {
        std::cerr << "Setting attitude euler rate failed: " << set_angle_euler_result << '\n';
        return 1;
    }

    // 订阅并打印当前位置数据
    telemetry.subscribe_position([](Telemetry::Position position) {
        std::cout << "Position: " << position.latitude_deg << ", " << position.longitude_deg
                  << " at altitude " << position.relative_altitude_m << " m\n";
    });
     
    // 解锁（arming）无人机
    std::cout << "Arming...\n";
    const Action::Result arm_result = action.arm();
    if (arm_result != Action::Result::Success) {
        std::cerr << "Arming failed: " << arm_result << '\n';
        return 1;
    }
    sleep_for(seconds(1));

    // 设置起飞高度为 5 米
    std::cout << "Setting takeoff altitude to 5 meters...\n";
    const Action::Result set_takeoff_alt_result = action.set_takeoff_altitude(5.0);
    if (set_takeoff_alt_result != Action::Result::Success) {
        std::cerr << "Setting takeoff altitude failed: " << set_takeoff_alt_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));
    
    // 发布起飞指令
    std::cout << "Taking off...\n";
    const Action::Result takeoff_result = action.takeoff();
    if (takeoff_result != Action::Result::Success) {
        std::cerr << "Takeoff failed: " << takeoff_result << '\n';
        return 1;
    }
    sleep_for(seconds(3));

    // 等待无人机响应 takeoff 指令
    while(telemetry.flight_mode() != Telemetry::FlightMode::Takeoff) {
        std::cout << "wait for takeoff...\n";
        sleep_for(seconds(1));
    }
    // 等待起飞完成
    while(telemetry.flight_mode() == Telemetry::FlightMode::Takeoff) {
        std::cout << "Taking off...\n";
        sleep_for(seconds(1));
    }
    sleep_for(seconds(2));

    // 获取当前位置并设置目标位置
    Telemetry::Position position = telemetry.position();
    Telemetry::EulerAngle euler = telemetry.attitude_euler();
    double target_latitude = position.latitude_deg + 0.0005;
    double target_longitude = position.longitude_deg + 0.0002;
    float target_altitude = position.relative_altitude_m;
    float target_yaw = atan2(target_longitude - position.longitude_deg, target_latitude - position.latitude_deg)/180;//不用于控制
    
    // 打印当前目标参数
    std::cout << "Current Parameters:\n";
    std::cout << "  Target Latitude:  " << std::fixed << std::setprecision(7) << target_latitude << " deg\n";
    std::cout << "  Target Longitude: " << std::fixed << std::setprecision(7) << target_longitude << " deg\n";
    std::cout << "  Target Altitude:  " << std::fixed << std::setprecision(2) << target_altitude << " m\n";
    std::cout << "  Target Yaw:       " << std::fixed << std::setprecision(2) << target_yaw << " deg\n";

    // 设置当前飞行速度为 5 米/秒
    std::cout << "Setting current speed to 5 m/s...\n";
    const Action::Result set_current_speed_result = action.set_current_speed(5.0);
    if (set_current_speed_result != Action::Result::Success) {
        std::cerr << "Setting current speed failed: " << set_current_speed_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));

    // 移动到目标位置
    std::cout << "Move to the targeted position " << '\n';
    const Action::Result goto_to_location_result = action.goto_location(target_latitude, target_longitude, target_altitude, target_yaw);
    if (goto_to_location_result != Action::Result::Success) {
        std::cerr << "Go to location failed: " << goto_to_location_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));


    // 等待无人机响应 go_to_location 指令
    std::cout << "wait for UAV to respond the go_to_location command...\n";
    while (telemetry.flight_mode() != Telemetry::FlightMode::Mission) {
        std::cout << "waiting for reponse...\n";
        sleep_for(seconds(1));
    }
    

    // 等待移动到目标位置
    std::cout << "wait for moving to target location...\n";
    while (telemetry.flight_mode() == Telemetry::FlightMode::Mission) {
        std::cout << "Moving to target location...\n";
        sleep_for(seconds(1));
    }
    sleep_for(seconds(2)); 

    // 悬停在目标位置
    std::cout << "hold at the target location...\n";
    const Action::Result hold_result = action.hold();
    if (hold_result != Action::Result::Success) {
        std::cerr << "Hold failed: " << hold_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));

    // 返回起飞点
    std::cout << "return to launch...\n";
    const Action::Result return_to_launch = action.return_to_launch();
    if (return_to_launch != Action::Result::Success) {
        std::cerr << "return to launch failed: " << return_to_launch << '\n';
        return 1;
    }
    sleep_for(seconds(2));
    

    // 等待无人机响应 return_to_launch 指令
    std::cout << "wait for UAV to respond the return_to_launch command...\n";
    while (telemetry.flight_mode() != Telemetry::FlightMode::ReturnToLaunch) {
        std::cout << "waiting for reponse...\n";
        sleep_for(seconds(1));
    }
    // 等待返回起飞点
    while (telemetry.flight_mode() == Telemetry::FlightMode::ReturnToLaunch) {
        std::cout << "returning home...\n";
        sleep_for(seconds(1));
    }
    sleep_for(seconds(2));

    // 发布降落指令
    std::cout << "land...\n";
    const Action::Result land_result = action.land();
    if (land_result != Action::Result::Success) {
        std::cerr << "Land failed: " << land_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));

    // 等待降落完成
    std::cout << "detect landing...\n";
    while (telemetry.in_air()) {
        std::cout << "Vehicle is in air\n";
        sleep_for(seconds(1));
    }

    // 已着陆
    std::cout << "Landed!\n";

    // 上锁无人机
    std::cout << "disarm the vehicle...\n";
    const Action::Result disarm_result = action.disarm();
    if (disarm_result != Action::Result::Success) {
        std::cerr << "Disarm failed: " << disarm_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));

    // 重启无人机
    std::cout << "reboot...\n";
    const Action::Result reboot_result = action.reboot();
    if (reboot_result != Action::Result::Success) {
        std::cerr << "Reboot failed: " << reboot_result << '\n';
        return 1;
    }

    return 0;
}
```

飞控会直接响应MAVSDK发出的MAVLink消息中所包含的指令，因此如果希望无人机能够顺序执行指令并完成指令，可以监听无人机指令执行的状态来判断指令是否响应或者执行完成。
```
    // 等待无人机响应 takeoff 指令
    while(telemetry.flight_mode() != Telemetry::FlightMode::Takeoff) {
        std::cout << "wait for takeoff...\n";
        sleep_for(seconds(1));
    }
    // 等待起飞完成
    while(telemetry.flight_mode() == Telemetry::FlightMode::Takeoff) {
        std::cout << "Taking off...\n";
        sleep_for(seconds(1));
    }
    sleep_for(seconds(2));
```
也可以使用 promise 和 future 来等待起飞完成
```
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
```
