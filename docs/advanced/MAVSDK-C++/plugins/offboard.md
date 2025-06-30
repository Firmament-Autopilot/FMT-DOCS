## API
| **API Name**                                                     | **API Usage**                                            | **Input**                                                                                  |
| ---------------------------------------------------------------- | -------------------------------------------------------- | ------------------------------------------------------------------------------------------ |
| `start()`                                                        | Start executing Offboard commands                        | None                                                                                       |
| `stop()`                                                         | Stop executing Offboard commands                         | None                                                                                       |
| `set_velocity_ned(VelocityNedYaw velocity_ned_yaw)`              | Set velocity and yaw in the NED coordinate system        | A struct containing velocity components (North, East, Down) and yaw angle in NED frame     |
| `set_acceleration_ned(AccelerationNed acceleration_ned)`         | Set acceleration in the NED coordinate system            | A struct containing acceleration components (North, East, Down) in NED frame               |
| `set_position_ned(PositionNedYaw position_ned_yaw)`              | Set position and yaw in the NED coordinate system        | A struct containing position (North, East, Down) and yaw angle in NED frame                |
| `set_position_global(PositionGlobalYaw position_global_yaw)`     | Set position and yaw in the global coordinate system     | A struct containing position (longitude, latitude, altitude) and yaw angle in global frame |
| `set_attitude(Attitude attitude)`                                | Set attitude                                             | A struct containing attitude (roll, pitch, yaw)                                            |
| `set_attitude_rate(AttitudeRate attitude_rate)`                  | Set attitude rate                                        | A struct containing attitude rates (roll rate, pitch rate, yaw rate)                       |
| `set_velocity_body(VelocityBodyYawspeed velocity_body_yawspeed)` | Set velocity and yaw speed in the body coordinate system | A struct containing velocity components (forward, right, down) and yaw speed in body frame |


## Example program

The following is an example program for testing the Offboard plugin API, demonstrating how to use MAVSDK to control the vehicle's motion and attitude in Offboard mode. The code sequentially performs the following operations: connecting to the vehicle, taking off, activating Offboard control, setting the vehicle's velocity, acceleration, position, global position, attitude angles, attitude angular rates (in NED frame), and body-frame velocity. After completing all Offboard commands, it lands the vehicle.
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

//  Print the program usage instructions
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
    // Check if the input parameters are valid
    if (argc != 2) {
        usage(argv[0]);
        return 1;
    }

    // Create Mavsdk object with the component type set to CompanionComputer
    Mavsdk mavsdk{Mavsdk::Configuration{Mavsdk::ComponentType::CompanionComputer}};
    ConnectionResult connection_result = mavsdk.add_any_connection(argv[1]); // add connection

    // Print error and exit if connection fails
    if (connection_result != ConnectionResult::Success) {
        std::cerr << "Connection failed: " << connection_result << '\n';
        return 1;
    }

    // Get the connected system; exit on timeout
    auto system = mavsdk.first_autopilot(3.0);
    if (!system) {
        std::cerr << "Timed out waiting for system\n";
        return 1;
    }
    
    // Instantiate plugin objects
    auto action = Action{system.value()};        // Action plugin
    auto offboard = Offboard{system.value()};    // Offboard plugin
    auto telemetry = Telemetry{system.value()};  // Telemetry plugin


    // Wait until the system reports all health checks as passed
    while (!telemetry.health_all_ok()) {
        std::cout << "Waiting for system to be ready\n";
        sleep_for(seconds(1));
    }
    std::cout << "System is ready\n";

    // Set telemetry update rate for position information to 1 Hz
    const auto set_position_global_rate_result = telemetry.set_rate_position(1.0);
    if (set_position_global_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting position rate failed: " << set_position_global_rate_result << '\n';
        return 1;
    }

    // Arm the drone
    const auto arm_result = action.arm();
    if (arm_result != Action::Result::Success) {
        std::cerr << "Arming failed: " << arm_result << '\n';
        return 1;
    }
    std::cout << "Armed\n";

    // Set takeoff altitude to 2 meters
    const auto set_takeoff_height_result = action.set_takeoff_altitude(2.0f);
    if (set_takeoff_height_result != Action::Result::Success) {
        std::cerr << "Setting takeoff altitude failed: " << set_takeoff_height_result << '\n';
        return 1;
    }

    // Takeoff
    const auto takeoff_result = action.takeoff();
    if (takeoff_result != Action::Result::Success) {
        std::cerr << "Takeoff failed: " << takeoff_result << '\n';
        return 1;
    }

    // Use promise and future to wait for takeoff completion
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

    // Wait up to 10 seconds, report error if takeoff times out
    in_air_future.wait_for(seconds(10));
    if (in_air_future.wait_for(seconds(15)) == std::future_status::timeout) {
        std::cerr << "Takeoff timed out.\n";
        return 1;
    }
    
    // An offboard command must be sent before starting offboard control, otherwise startup will fail
    const Offboard::VelocityNedYaw stay{};  // Empty VelocityNedYaw command
    offboard.set_velocity_ned(stay);

    // Start offboard control
    Offboard::Result offboard_result = offboard.start();
    if (offboard_result != Offboard::Result::Success) {
        std::cerr << "Offboard start failed: " << offboard_result << '\n';
        return 1;
    }

    std::cout << "velocity ned\n";
    // Set velocity in the NED coordinate system
    Offboard::VelocityNedYaw vel_ned{};
    vel_ned.yaw_deg = 270.0f;   // Set yaw angle to 270 degrees
    vel_ned.down_m_s = -1.0f;   // Set downward velocity to -1 m/s
    vel_ned.east_m_s = -1.0f;   // Set eastward velocity to -1 m/s
    vel_ned.north_m_s = 1.0f;   // Set northward velocity to 1 m/s
    offboard.set_velocity_ned(vel_ned);  // Set velocity
    std::cout << vel_ned << '\n';
    sleep_for(seconds(5));


    std::cout << "acceleration ned\n";
    // Set acceleration in the NED coordinate system
    Offboard::AccelerationNed acc_ned{};
    acc_ned.down_m_s2 = -1.0f;  // Set downward acceleration to -1 m/s²
    acc_ned.east_m_s2 = -1.0f;  // Set eastward acceleration to -1 m/s²
    acc_ned.north_m_s2 = 1.0f;  // Set northward acceleration to 1 m/s²
    offboard.set_acceleration_ned(acc_ned);  // Set acceleration
    std::cout << acc_ned << '\n';
    sleep_for(seconds(5));

    std::cout << "position ned\n";
    // Set desired position in the NED coordinate system
    Offboard::PositionNedYaw pos_ned{};
    pos_ned.yaw_deg = 0.0f;    // Set yaw angle to 0 degrees
    pos_ned.down_m = -15.0f;   // Set downward position to -15 meters
    pos_ned.east_m = -10.0f;   // Set eastward position to -10 meters
    pos_ned.north_m = 10.0f;   // Set northward position to 10 meters
    offboard.set_position_ned(pos_ned);  // Set position
    std::cout << pos_ned << '\n';
    sleep_for(seconds(15));

    std::cout << "get position global\n";
    // Get global position from telemetry
    auto position_global = telemetry.position();
    std::cout << "latitude: " << position_global.latitude_deg << '\n';
    std::cout << "longitude: " << position_global.longitude_deg << '\n';
    std::cout << "absolute altitude: " << position_global.absolute_altitude_m << '\n';
    std::cout << "relative altitude: " << position_global.relative_altitude_m << '\n';

    std::cout << "set a target position global\n";
    // Set target position in global coordinates
    Offboard::PositionGlobalYaw pos_global{};
    pos_global.alt_m = 20.0f;  // Set absolute altitude to 20 meters
    pos_global.lat_deg = position_global.latitude_deg + 0.0001f;  // Latitude (slightly offset)
    pos_global.lon_deg = position_global.longitude_deg + 0.0001f; // Longitude (slightly offset)
    pos_global.yaw_deg = 90.0f;  // Set target yaw angle to 90 degrees
    std::cout << "position global\n";
    offboard.set_position_global(pos_global);  // Set the target global position
    std::cout << pos_global << '\n';
    sleep_for(seconds(15));

    std::cout << "attitude\n";
    // Set attitude
    Offboard::Attitude att{};
    att.pitch_deg = 10.0f;       // Set pitch angle to 10 degrees
    att.roll_deg = 10.0f;        // Set roll angle to 10 degrees
    att.yaw_deg = 0.0f;          // Set yaw angle to 0 degrees
    att.thrust_value = 0.48f;    // Set throttle (thrust) value
    offboard.set_attitude(att);  // Apply the attitude
    std::cout << att << '\n';
    sleep_for(seconds(10));

    std::cout << "attitude rate\n";
    // Set attitude rate (angular velocity)
    Offboard::AttitudeRate att_rate{};
    att_rate.pitch_deg_s = 1.0f;     // Pitch rate (degrees per second)
    att_rate.roll_deg_s = 1.0f;      // Roll rate (degrees per second)
    att_rate.yaw_deg_s = 1.0f;       // Yaw rate (degrees per second)
    att_rate.thrust_value = 0.465f;  // Set throttle (thrust) value
    offboard.set_attitude_rate(att_rate);  // Apply the attitude rates
    std::cout << att_rate << '\n';
    sleep_for(seconds(10));

    std::cout << "velocity body\n";
    // Set velocity in the body coordinate frame
    Offboard::VelocityBodyYawspeed vel_body{};
    vel_body.down_m_s = -1.0f;         // Downward velocity -1 m/s (going up)
    vel_body.forward_m_s = 1.0f;       // Forward velocity 1 m/s
    vel_body.right_m_s = 1.0f;         // Rightward velocity 1 m/s
    vel_body.yawspeed_deg_s = 3.0f;    // Yaw rate 3 degrees per second
    offboard.set_velocity_body(vel_body);  // Set body frame velocity and yaw speed
    std::cout << vel_body << '\n';
    sleep_for(seconds(120));


    // Landing operation
    const auto land_result = action.land();
    if (land_result != Action::Result::Success) {
        std::cerr << "Landing failed: " << land_result << '\n';
        return 1;
    }

    // Check if the vehicle is still in the air
    while (telemetry.in_air()) {
        std::cout << "Vehicle is landing...\n";
        sleep_for(seconds(1));
    }
    std::cout << "Landed!\n";

    // Wait for the vehicle to automatically disarm (lock)
    sleep_for(seconds(3));
    std::cout << "Finished...\n";

    return 0;
}
```