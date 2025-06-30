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
go_to_location(double latitude_deg, double longitude_deg, float absolute_altitude_m, float yaw_deg)        | send go to given location command     | The desired longitude, latitude, altitude, and the yaw angle during operation.
set_takeoff_altitude(float altitude)  | set the takeoff altitude              | The desired takeoff altitude.
set_current_speed(float speed_m_s)     | set flight speed                      | The desired cruising speed.


> According to FMT’s handling of the `go_to_location(double latitude_deg, double longitude_deg, float absolute_altitude_m, float yaw_deg)` function, the parameter `yaw_deg` is not used as a control input to adjust the vehicle’s yaw during the movement to the specified waypoint.

## Example program

The following is an example program for testing the action plugin API. The command sequence sent by the program is: arm, take off, move to a specified location, hover at that location, return to the takeoff point, land, disarm, and finally reboot.

```
//Include the Action and Telemetry plugin headers in the header file.

#include <chrono>  
#include <cstdint> 
#include <mavsdk/mavsdk.h> 
#include <mavsdk/plugins/action/action.h> // Include the MAVSDK Action plugin library, used to perform drone actions such as takeoff, landing, and hovering
#include <mavsdk/plugins/telemetry/telemetry.h> // Include the MAVSDK Telemetry plugin library, used to obtain drone telemetry data such as position, attitude, and velocity
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

    

    // Get the first available autopilot system
    auto system = mavsdk.first_autopilot(3.0);
    if (!system) {
        std::cerr << "Timed out waiting for system\n";
        return 1;
    }

    // Instantiate plugins: telemetry (for telemetry data) and action (for drone commands)    auto telemetry = Telemetry{system.value()};
    auto action = Action{system.value()};

    // Set telemetry data update rate to 1 Hz
    const auto set_pos_rate_result = telemetry.set_rate_position(1.0);
    if (set_pos_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting position rate failed: " << set_pos_rate_result << '\n';
        return 1;
    }
    
    // Set attitude Euler angles data update rate to 1 Hz
    const auto set_angle_euler_result = telemetry.set_rate_attitude_euler(1.0);
    if (set_angle_euler_result != Telemetry::Result::Success) {
        std::cerr << "Setting attitude euler rate failed: " << set_angle_euler_result << '\n';
        return 1;
    }

    // Subscribe to and print current position data
    telemetry.subscribe_position([](Telemetry::Position position) {
        std::cout << "Position: " << position.latitude_deg << ", " << position.longitude_deg
                  << " at altitude " << position.relative_altitude_m << " m\n";
    });
     
    // Arm the drone
    std::cout << "Arming...\n";
    const Action::Result arm_result = action.arm();
    if (arm_result != Action::Result::Success) {
        std::cerr << "Arming failed: " << arm_result << '\n';
        return 1;
    }
    sleep_for(seconds(1));

    // Set takeoff altitude to 5 meters
    std::cout << "Setting takeoff altitude to 5 meters...\n";
    const Action::Result set_takeoff_alt_result = action.set_takeoff_altitude(5.0);
    if (set_takeoff_alt_result != Action::Result::Success) {
        std::cerr << "Setting takeoff altitude failed: " << set_takeoff_alt_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));
    
    // Send takeoff command
    std::cout << "Taking off...\n";
    const Action::Result takeoff_result = action.takeoff();
    if (takeoff_result != Action::Result::Success) {
        std::cerr << "Takeoff failed: " << takeoff_result << '\n';
        return 1;
    }
    sleep_for(seconds(3));

    // Wait for the drone to respond to the takeoff command
    while(telemetry.flight_mode() != Telemetry::FlightMode::Takeoff) {
        std::cout << "wait for takeoff...\n";
        sleep_for(seconds(1));
    }
    // Wait for takeoff to complete
    while(telemetry.flight_mode() == Telemetry::FlightMode::Takeoff) {
        std::cout << "Taking off...\n";
        sleep_for(seconds(1));
    }
    sleep_for(seconds(2));

    // Get current position and set target position
    Telemetry::Position position = telemetry.position();
    Telemetry::EulerAngle euler = telemetry.attitude_euler();
    double target_latitude = position.latitude_deg + 0.0005;
    double target_longitude = position.longitude_deg + 0.0002;
    float target_altitude = position.relative_altitude_m;
    float target_yaw = atan2(target_longitude - position.longitude_deg, target_latitude - position.latitude_deg)/180;//不用于控制
    
    // Print current target parameters
    std::cout << "Current Parameters:\n";
    std::cout << "  Target Latitude:  " << std::fixed << std::setprecision(7) << target_latitude << " deg\n";
    std::cout << "  Target Longitude: " << std::fixed << std::setprecision(7) << target_longitude << " deg\n";
    std::cout << "  Target Altitude:  " << std::fixed << std::setprecision(2) << target_altitude << " m\n";
    std::cout << "  Target Yaw:       " << std::fixed << std::setprecision(2) << target_yaw << " deg\n";

    // Set current flight speed to 5 meters per second
    std::cout << "Setting current speed to 5 m/s...\n";
    const Action::Result set_current_speed_result = action.set_current_speed(5.0);
    if (set_current_speed_result != Action::Result::Success) {
        std::cerr << "Setting current speed failed: " << set_current_speed_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));

    // Move to the target position
    std::cout << "Move to the targeted position " << '\n';
    const Action::Result goto_to_location_result = action.goto_location(target_latitude, target_longitude, target_altitude, target_yaw);
    if (goto_to_location_result != Action::Result::Success) {
        std::cerr << "Go to location failed: " << goto_to_location_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));


    // Wait for the drone to respond to the `go_to_location` command
    std::cout << "wait for UAV to respond the go_to_location command...\n";
    while (telemetry.flight_mode() != Telemetry::FlightMode::Mission) {
        std::cout << "waiting for reponse...\n";
        sleep_for(seconds(1));
    }
    

    // Wait until the drone reaches the target position
    std::cout << "wait for moving to target location...\n";
    while (telemetry.flight_mode() == Telemetry::FlightMode::Mission) {
        std::cout << "Moving to target location...\n";
        sleep_for(seconds(1));
    }
    sleep_for(seconds(2)); 

    // Hover at the target position
    std::cout << "hold at the target location...\n";
    const Action::Result hold_result = action.hold();
    if (hold_result != Action::Result::Success) {
        std::cerr << "Hold failed: " << hold_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));

    // Return to the takeoff point
    std::cout << "return to launch...\n";
    const Action::Result return_to_launch = action.return_to_launch();
    if (return_to_launch != Action::Result::Success) {
        std::cerr << "return to launch failed: " << return_to_launch << '\n';
        return 1;
    }
    sleep_for(seconds(2));
    

    // Wait for the drone to respond to the return_to_launch command
    std::cout << "wait for UAV to respond the return_to_launch command...\n";
    while (telemetry.flight_mode() != Telemetry::FlightMode::ReturnToLaunch) {
        std::cout << "waiting for reponse...\n";
        sleep_for(seconds(1));
    }
    // Wait until the drone returns to the takeoff point
    while (telemetry.flight_mode() == Telemetry::FlightMode::ReturnToLaunch) {
        std::cout << "returning home...\n";
        sleep_for(seconds(1));
    }
    sleep_for(seconds(2));

    // Send the land command
    std::cout << "land...\n";
    const Action::Result land_result = action.land();
    if (land_result != Action::Result::Success) {
        std::cerr << "Land failed: " << land_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));

    // Wait for landing to complete
    std::cout << "detect landing...\n";
    while (telemetry.in_air()) {
        std::cout << "Vehicle is in air\n";
        sleep_for(seconds(1));
    }

    // Landed
    std::cout << "Landed!\n";

    // Disarm the drone
    std::cout << "disarm the vehicle...\n";
    const Action::Result disarm_result = action.disarm();
    if (disarm_result != Action::Result::Success) {
        std::cerr << "Disarm failed: " << disarm_result << '\n';
        return 1;
    }
    sleep_for(seconds(2));

    // Reboot the drone
    std::cout << "reboot...\n";
    const Action::Result reboot_result = action.reboot();
    if (reboot_result != Action::Result::Success) {
        std::cerr << "Reboot failed: " << reboot_result << '\n';
        return 1;
    }

    return 0;
}
```

The flight controller directly responds to the commands contained in the MAVLink messages sent by MAVSDK. Therefore, to ensure that the drone executes commands sequentially and completes them, you can monitor the execution status of each command to determine whether it has been acknowledged or completed.

```
    while(telemetry.flight_mode() != Telemetry::FlightMode::Takeoff) {
        std::cout << "wait for takeoff...\n";
        sleep_for(seconds(1));
    }

    while(telemetry.flight_mode() == Telemetry::FlightMode::Takeoff) {
        std::cout << "Taking off...\n";
        sleep_for(seconds(1));
    }
    sleep_for(seconds(2));
```
You can also use **promise** and **future** to wait for the takeoff to complete.

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
