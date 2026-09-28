## API
API Name                    | API Usage             | Input        
-----                       | --------------        | -------------- 
set_rate_position_velocity_ned()    | 设置‘GPS信息’更新频率 | frequency (Hz)
set_rate_position()                 | 设置‘位置信息’更新频率 | frequency (Hz)
set_rate_home()                     | 设置‘Home位置’更新频率 | frequency (Hz)
set_rate_in_air()                   | 设置‘在空中’更新频率   | frequency (Hz)
set_rate_attitude_quaternion()      | 设置‘姿态四元数’更新频率 | frequency (Hz)
set_rate_attitude_euler()           | 设置‘姿态欧拉角’更新频率 | frequency (Hz)
set_rate_velocity_ned()             | 设置‘速度NED’更新频率   | frequency (Hz)
set_rate_imu()                      | 设置‘IMU’更新频率       | frequency (Hz)
set_rate_scaled_imu()               | 设置‘缩放IMU’更新频率   | frequency (Hz)
set_rate_gps_info()                 | 设置‘GPS信息’更新频率   | frequency (Hz)
set_rate_unix_epoch_time()          | 设置‘Unix时间辍’更新频率 | frequency (Hz)
set_rate_altitude()                 | 设置‘高度’更新频率       | frequency (Hz)
position_velocity_ned()             | 获取‘位置速度NED’       | None
position()                          | 获取‘位置’              | None
home()                              | 获取‘Home位置’          | None
in_air()                            | 获取‘在空中’            | None
attitude_quaternion()               | 获取‘姿态四元数’        | None
attitude_euler()                    | 获取‘姿态欧拉角’        | None
velocity_ned()                      | 获取‘速度NED’           | None
imu()                               | 获取‘IMU信息’               | None
scaled_imu()                        | 获取‘缩放的IMU信息’           | None
gps_info()                          | 获取‘GPS信息’           | None
unix_epoch_time()                   | 获取‘Unix时间辍’      | None
subscribe_position()                | 订阅‘位置’              | None
subscribe_home()                    | 订阅‘Home位置’          | None
subscribe_in_air()                  | 订阅‘在空中’            | None
subscribe_attitude_quaternion()     | 订阅‘姿态四元数’        | None
subscribe_attitude_euler()          | 订阅‘姿态欧拉角’        | None
subscribe_velocity_ned()            | 订阅‘速度NED’           | None
subscribe_imu()                     | 订阅‘IMU’               | None
subscribe_scaled_imu()              | 订阅‘缩放IMU’           | None
subscribe_gps_info()                | 订阅‘GPS信息’           | None
subscribe_unix_epoch_time()         | 订阅‘Unix时间辍’      | None
subscribe_altitude()                | 订阅‘高度’              | None
subscribe_position_velocity_ned()   | 订阅‘位置速度NED’       | None

## 测试程序

下面是一份用于测试Telemetry插件API的示例程序,测试了所有支持的Telemetry消息的更新频率设置函数、获取函数以及订阅函数。

在运行程序之前，推荐通过地面站在SIH仿真下使无人机在Position模式下起飞并设定一个航点，或者也可以参照[action](content_ch/advanced/MAVSDK-C++/plugins/action.md)中，通过action插件控制SIH仿真下的无人机移动，来观察Telemetry数据的更新。

```
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

    auto system = mavsdk.first_autopilot(3.0);
    if (!system) {
        std::cerr << "Timed out waiting for system\n";
        return 1;
    }

    // Instantiate plugins.
    auto telemetry = Telemetry{system.value()};

    
    // 以1Hz的频率监听遥感信息
    const auto set_position_velocity_ned_rate_result = telemetry.set_rate_position_velocity_ned(1.0);
    if (set_position_velocity_ned_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting position velocity NED rate failed: " << set_position_velocity_ned_rate_result << '\n';
        return 1;
    }

    const auto set_pos_rate_result = telemetry.set_rate_position(1.0);
    if (set_pos_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting position rate failed: " << set_pos_rate_result << '\n';
        return 1;
    }

    const auto set_home_rate_result = telemetry.set_rate_home(1.0);
    if (set_home_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting home position rate failed: " << set_home_rate_result << '\n';
        return 1;
    }

    const auto set_in_air_rate_result = telemetry.set_rate_in_air(1.0);
    if (set_in_air_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting in air rate failed: " << set_in_air_rate_result << '\n';
        return 1;
    }

    const auto set_att_quat_rate_result = telemetry.set_rate_attitude_quaternion(1.0);
    if (set_att_quat_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting attitude quaternion rate failed: " << set_att_quat_rate_result << '\n';
        return 1;
    }

    const auto set_att_euler_rate_result = telemetry.set_rate_attitude_euler(1.0);
    if (set_att_euler_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting attitude euler rate failed: " << set_att_euler_rate_result << '\n';
        return 1;
    }

    const auto set_velocity_ned_rate_result = telemetry.set_rate_velocity_ned(1.0);
    if (set_velocity_ned_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting velocity NED rate failed: " << set_velocity_ned_rate_result << '\n';
        return 1;
    }

    const auto set_imu_rate_result = telemetry.set_rate_imu(1.0);
    if (set_imu_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting IMU rate failed: " << set_imu_rate_result << '\n';
        return 1;
    }

    const auto set_scaled_imu_rate_result = telemetry.set_rate_scaled_imu(1.0);
    if (set_scaled_imu_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting scaled IMU rate failed: " << set_scaled_imu_rate_result << '\n';
        return 1;
    }

    const auto set_gps_info_rate_result = telemetry.set_rate_gps_info(1.0);
    if (set_gps_info_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting GPS info rate failed: " << set_gps_info_rate_result << '\n';
        return 1;
    }

    const auto set_distance_sensor_rate_result = telemetry.set_rate_distance_sensor(1.0);
    if (set_distance_sensor_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting distance sensor rate failed: " << set_distance_sensor_rate_result << '\n';
        return 1;
    }

    const auto set_unix_epoch_time_rate_result = telemetry.set_rate_unix_epoch_time(1.0);
    if (set_unix_epoch_time_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting unix epoch time rate failed: " << set_unix_epoch_time_rate_result << '\n';
        return 1;
    }

    const auto set_altitude_rate_result = telemetry.set_rate_altitude(1.0);
    if (set_altitude_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting altitude rate failed: " << set_altitude_rate_result << '\n';
        return 1;
    }

    // 打印一次遥感信息
    const auto position_velocity_ned = telemetry.position_velocity_ned();
    std::cout << "Position Velocity NED:"
              << "\n  Position North: " << std::fixed << std::setprecision(2) << position_velocity_ned.position.north_m << " m"
              << "\n  Position East:  " << std::fixed << std::setprecision(2) << position_velocity_ned.position.east_m << " m"
              << "\n  Position Down:  " << std::fixed << std::setprecision(2) << position_velocity_ned.position.down_m << " m"
              << "\n  Velocity North: " << std::fixed << std::setprecision(2) << position_velocity_ned.velocity.north_m_s << " m/s"
              << "\n  Velocity East:  " << std::fixed << std::setprecision(2) << position_velocity_ned.velocity.east_m_s << " m/s"
              << "\n  Velocity Down:  " << std::fixed << std::setprecision(2) << position_velocity_ned.velocity.down_m_s << " m/s\n";

    const auto position = telemetry.position();
    std::cout << "Position:"
              << "\n  Latitude:  " << std::fixed << std::setprecision(7) << position.latitude_deg << " deg"
              << "\n  Longitude: " << std::fixed << std::setprecision(7) << position.longitude_deg << " deg"
              << "\n  Rel Alt:   " << std::fixed << std::setprecision(2) << position.relative_altitude_m << " m"
              << "\n  Abs Alt:   " << std::fixed << std::setprecision(2) << position.absolute_altitude_m << " m\n";

    const auto home = telemetry.home();
    std::cout << "Home Position:"
              << "\n  Latitude:  " << std::fixed << std::setprecision(7) << home.latitude_deg << " deg"
              << "\n  Longitude: " << std::fixed << std::setprecision(7) << home.longitude_deg << " deg"
              << "\n  Altitude:  " << std::fixed << std::setprecision(2) << home.absolute_altitude_m << " m\n";

    const auto in_air = telemetry.in_air();
    std::cout << "In Air: " << (in_air ? "Yes" : "No") << "\n";

    const auto attitude_quaternion = telemetry.attitude_quaternion();
    std::cout << "Attitude Quaternion:"
              << "\n  w: " << std::fixed << std::setprecision(4) << attitude_quaternion.w
              << "\n  x: " << std::fixed << std::setprecision(4) << attitude_quaternion.x
              << "\n  y: " << std::fixed << std::setprecision(4) << attitude_quaternion.y
              << "\n  z: " << std::fixed << std::setprecision(4) << attitude_quaternion.z << "\n";

    const auto attitude_euler = telemetry.attitude_euler();
    std::cout << "Attitude Euler:"
              << "\n  Roll:  " << std::fixed << std::setprecision(2) << attitude_euler.roll_deg << " deg"
              << "\n  Pitch: " << std::fixed << std::setprecision(2) << attitude_euler.pitch_deg << " deg"
              << "\n  Yaw:   " << std::fixed << std::setprecision(2) << attitude_euler.yaw_deg << " deg\n";

    const auto velocity_ned = telemetry.velocity_ned();
    std::cout << "Velocity:"
              << "\n  North: " << std::fixed << std::setprecision(2) << velocity_ned.north_m_s << " m/s"
              << "\n  East:  " << std::fixed << std::setprecision(2) << velocity_ned.east_m_s << " m/s"
              << "\n  Down:  " << std::fixed << std::setprecision(2) << velocity_ned.down_m_s << " m/s\n";

    const auto imu = telemetry.imu();
    std::cout << "IMU Acceleration:"
              << "\n  X: " << std::fixed << std::setprecision(2) << imu.acceleration_frd.forward_m_s2 << " m/s^2"
              << "\n  Y: " << std::fixed << std::setprecision(2) << imu.acceleration_frd.right_m_s2 << " m/s^2"
              << "\n  Z: " << std::fixed << std::setprecision(2) << imu.acceleration_frd.down_m_s2 << " m/s^2\n";

    const auto scaled_imu = telemetry.scaled_imu();
    std::cout << "Scaled IMU:"
              << "\n  X: " << std::fixed << std::setprecision(2) << scaled_imu.acceleration_frd.forward_m_s2 << " m/s^2"
              << "\n  Y: " << std::fixed << std::setprecision(2) << scaled_imu.acceleration_frd.right_m_s2 << " m/s^2"
              << "\n  Z: " << std::fixed << std::setprecision(2) << scaled_imu.acceleration_frd.down_m_s2 << " m/s^2\n";

    const auto gps_info = telemetry.gps_info();
    std::cout << "GPS Info:"
              << "\n  Num Satellites: " << std::setw(2) << std::right << gps_info.num_satellites
              << "\n  Fix Type:       " << std::setw(2) << std::right << static_cast<int>(gps_info.fix_type) << "\n\n";

    const auto unix_epoch_time = telemetry.unix_epoch_time();
    std::cout << "Unix Epoch Time: " << unix_epoch_time << " us\n";

    const auto altitude = telemetry.altitude();
    std::cout << "Altitude:"
              << "\n  Monotonic: " << std::fixed << std::setprecision(2) << altitude.altitude_monotonic_m << " m(not assigned)"
              << "\n  AMSL:      " << std::fixed << std::setprecision(2) << altitude.altitude_amsl_m << " m"
              << "\n  Local:     " << std::fixed << std::setprecision(2) << altitude.altitude_local_m << " m"
              << "\n  Relative:  " << std::fixed << std::setprecision(2) << altitude.altitude_relative_m << " m"
              << "\n  Terrain:   " << std::fixed << std::setprecision(2) << altitude.altitude_terrain_m << " m"
              << "\n  Bottom Clearance: " << std::fixed << std::setprecision(2) << altitude.bottom_clearance_m << " m(not assigned)\n";

    
    //监听并按照更新速率打印遥感信息
    telemetry.subscribe_position_velocity_ned([](Telemetry::PositionVelocityNed position_velocity_ned) {
        std::cout << "Position Velocity NED:"
                  << "\n  Position North: " << std::fixed << std::setprecision(2) << position_velocity_ned.position.north_m << " m"
                  << "\n  Position East:  " << std::fixed << std::setprecision(2) << position_velocity_ned.position.east_m << " m"
                  << "\n  Position Down:  " << std::fixed << std::setprecision(2) << position_velocity_ned.position.down_m << " m"
                  << "\n  Velocity North: " << std::fixed << std::setprecision(2) << position_velocity_ned.velocity.north_m_s << " m/s"
                  << "\n  Velocity East:  " << std::fixed << std::setprecision(2) << position_velocity_ned.velocity.east_m_s << " m/s"
                  << "\n  Velocity Down:  " << std::fixed << std::setprecision(2) << position_velocity_ned.velocity.down_m_s << " m/s\n";
    });

    telemetry.subscribe_position([](Telemetry::Position position) {
        std::cout << "Position:"
                  << "\n  Latitude:  " << std::fixed << std::setprecision(7) << position.latitude_deg << " deg"
                  << "\n  Longitude: " << std::fixed << std::setprecision(7) << position.longitude_deg << " deg"
                  << "\n  Rel Alt:   " << std::fixed << std::setprecision(2) << position.relative_altitude_m << " m"
                  << "\n  Abs Alt:   " << std::fixed << std::setprecision(2) << position.absolute_altitude_m << " m\n";
    });

    telemetry.subscribe_home([](Telemetry::Position home) {
        std::cout << "Home Position:"
                  << "\n  Latitude:  " << std::fixed << std::setprecision(7) << home.latitude_deg << " deg"
                  << "\n  Longitude: " << std::fixed << std::setprecision(7) << home.longitude_deg << " deg"
                  << "\n  Altitude:  " << std::fixed << std::setprecision(2) << home.absolute_altitude_m << " m\n";
    });

    telemetry.subscribe_in_air([](bool in_air) {
        std::cout << "In Air: " << (in_air ? "Yes" : "No") << "\n";
    });

    telemetry.subscribe_attitude_quaternion([](Telemetry::Quaternion quaternion) {
        std::cout << "Attitude Quaternion:"
                  << "\n  w: " << std::fixed << std::setprecision(4) << quaternion.w
                  << "\n  x: " << std::fixed << std::setprecision(4) << quaternion.x
                  << "\n  y: " << std::fixed << std::setprecision(4) << quaternion.y
                  << "\n  z: " << std::fixed << std::setprecision(4) << quaternion.z << "\n";
    });

    telemetry.subscribe_attitude_euler([](Telemetry::EulerAngle euler) {
        std::cout << "Attitude Euler:"
                  << "\n  Roll:  " << std::fixed << std::setprecision(2) << euler.roll_deg << " deg"
                  << "\n  Pitch: " << std::fixed << std::setprecision(2) << euler.pitch_deg << " deg"
                  << "\n  Yaw:   " << std::fixed << std::setprecision(2) << euler.yaw_deg << " deg\n";
    });

    telemetry.subscribe_velocity_ned([](Telemetry::VelocityNed velocity) {
        std::cout << "Velocity:"
                  << "\n  North: " << std::fixed << std::setprecision(2) << velocity.north_m_s << " m/s"
                  << "\n  East:  " << std::fixed << std::setprecision(2) << velocity.east_m_s << " m/s"
                  << "\n  Down:  " << std::fixed << std::setprecision(2) << velocity.down_m_s << " m/s\n";
    });

    telemetry.subscribe_imu([](Telemetry::Imu imu) {
        std::cout << "IMU Acceleration:"
                  << "\n  X: " << std::fixed << std::setprecision(2) << imu.acceleration_frd.forward_m_s2 << " m/s^2"
                  << "\n  Y: " << std::fixed << std::setprecision(2) << imu.acceleration_frd.right_m_s2 << " m/s^2"
                  << "\n  Z: " << std::fixed << std::setprecision(2) << imu.acceleration_frd.down_m_s2 << " m/s^2\n";
    });

    telemetry.subscribe_scaled_imu([](Telemetry::Imu scaled_imu) {
        std::cout << "Scaled IMU:"
                  << "\n  X: " << std::fixed << std::setprecision(2) << scaled_imu.acceleration_frd.forward_m_s2 << " m/s^2"
                  << "\n  Y: " << std::fixed << std::setprecision(2) << scaled_imu.acceleration_frd.right_m_s2 << " m/s^2"
                  << "\n  Z: " << std::fixed << std::setprecision(2) << scaled_imu.acceleration_frd.down_m_s2 << " m/s^2\n";
    });

    telemetry.subscribe_gps_info([](Telemetry::GpsInfo gps_info) {
        std::cout << "GPS Info:"
                  << "\n  Num Satellites: " << std::setw(2) << std::right << gps_info.num_satellites
                  << "\n  Fix Type:       " << std::setw(2) << std::right << static_cast<int>(gps_info.fix_type) << "\n\n";
    });

    telemetry.subscribe_distance_sensor([](Telemetry::DistanceSensor distance_sensor) {
        std::cout << "Distance Sensor:"
                  << "\n  Min Distance: " << std::fixed << std::setprecision(2) << distance_sensor.minimum_distance_m << " m"
                  << "\n  Max Distance: " << std::fixed << std::setprecision(2) << distance_sensor.maximum_distance_m << " m"
                  << "\n  Current Distance: " << std::fixed << std::setprecision(2) << distance_sensor.current_distance_m << " m\n";
    });

    telemetry.subscribe_unix_epoch_time([](uint64_t unix_epoch_time) {
        std::cout << "Unix Epoch Time: " << unix_epoch_time << " us\n";
    });

    telemetry.subscribe_altitude([](Telemetry::Altitude altitude) {
        std::cout << "Altitude:"
                  << "\n  Monotonic: " << std::fixed << std::setprecision(2) << altitude.altitude_monotonic_m << " m(not assigned)"
                  << "\n  AMSL:      " << std::fixed << std::setprecision(2) << altitude.altitude_amsl_m << " m"
                  << "\n  Local:     " << std::fixed << std::setprecision(2) << altitude.altitude_local_m << " m"
                  << "\n  Relative:  " << std::fixed << std::setprecision(2) << altitude.altitude_relative_m << " m"
                  << "\n  Terrain:   " << std::fixed << std::setprecision(2) << altitude.altitude_terrain_m << " m"
                  << "\n  Bottom Clearance: " << std::fixed << std::setprecision(2) << altitude.bottom_clearance_m << " m(not assigned)\n";
    });


    sleep_for(seconds(50));
    std::cout << "Finished...\n";

    return 0;
}
```