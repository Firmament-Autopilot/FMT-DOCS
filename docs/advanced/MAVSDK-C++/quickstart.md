## MAVSDK introduction   
MAVSDK is a cross-platform collection of development libraries that simplifies interaction with MAVLink systems by providing easy-to-use APIs. It is suitable for the development of drones, ground control stations, and mobile devices, enabling functions such as information retrieval, telemetry, and mission control.

## Environment configuration
- Linux

    Let's take installing and using MAVSDK on Ubuntu 20.04 as an example.

    1. Installing dependencies and toolkits
        ```
        sudo apt-get update -y
        sudo apt-get install python3-pip -y
        sudo apt-get install cmake build-essential colordiff git doxygen -y
        ```
    2. Clone the MAVSDK (version 2.12) repository to the local machine.
        ```
        git clone -b v2.12 https://github.com/mavlink/MAVSDK.git --recursive --shallow-submodule
        ```
    3. Build the C++ library (Debug/Release) using the following commands.
        ```
        cd MAVSDK
        cmake -DCMAKE_BUILD_TYPE=Debug -DBUILD_SHARED_LIBS=ON -Bbuild/default -H.
        cmake --build build/default
        ```
        > The Debug version is suitable for debugging and development stages, while the Release version is appropriate for testing and deployment stages.
    4. Install the SDK (Software Development Kit)
        - Install system-wide (recommended)
            - System-wide installation copies the SDK’s header files and binaries to the platform’s standard system locations (for example, on Ubuntu Linux, this is `/usr/local/`).
            - Install the SDK system-wide.
                ```
                sudo cmake --build build/default --target install # sudo is required to install to system directories!

                # only for first installation 
                sudo ldconfig  # update linker cache
                ```
                > System-wide installation will overwrite any previously installed SDK versions.
        - Install locally
            - Local installation copies the SDK’s header files and libraries into a user-specified location within the SDK source directory.
            - On Linux/macOS, use the `CMAKE_INSTALL_PREFIX` variable to specify a path relative to the folder where `cmake` is called (or an absolute path). For example, to install into the `MAVSDK/install/` folder, you can use the following command:
            ```
            cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=ON -DCMAKE_INSTALL_PREFIX=install -Bbuild/default -H.
            cmake --build build/default --target install
            ```
            > If you ran cmake without specifying ```CMAKE_INSTALL_PREFIX```, you can clear the built files by running:```rm -rf build/default```

## Connect to Flight Controller
- MAVProxy configuration

   It is recommended to set the baud rate to `115200` for the **chan = 1** device configuration in the **Mavproxy** section of the **sysconfig.toml** file.
    ```
    # Mavproxy Device Configuration
    [mavproxy]
        [[mavproxy.devices]]
        chan = 0
        type = "serial"
        name = "serial1"
        baudrate = 57600

        [[mavproxy.devices]]
        chan = 0
        type = "usb"
        name = "usbd0"
        auto-switch = true

        [[mavproxy.devices]]
        chan = 1
        type = "serial"
        name = "serial2"
        baudrate = 115200
    ```
- Port connection

    Taking the FMT official flight controller S1 as an example, connect the computer and the flight controller’s `TELEM2` port using a TTL-to-USB cable. After connecting, open the console and enter:
    ```
    ls /dev/ttyUSB*
    ```
    Check the USB port name
    ```
    rock@rock-ThinkBook-16-G6-IMH:~$ ls /dev/ttyUSB*
    /dev/ttyUSB0
    ```
    The port name is `/dev/ttyUSB0`.
    > PS：If no port number is displayed, please configure the USB driver according to the relevant tutorial.

    Then, enter the following command in the console:

    ```
    cu -l /dev/ttyUSB0 -s 115200
    ```
    If heartbeat messages are received, it means the flight controller and computer are properly connected and can transmit MAVLink messages.
    ```
    rock@rock-ThinkBook-16-G6-IMH:~$ cu -l /dev/ttyUSB0 -s 115200
    Connected.
    �	�
             �A�	�
                     ���	�
                             �U�	�
                                     ���	�
                                             �i�	�
                                                     ���	�
                                                             �}�	�
                                                                     �>�	�
                                                                             ���	�
                                                                                   �
    ```
    > If the USB device lacks read/write permissions and shows a "permission denied" error, run the following command in the terminal to grant read/write access:```sudo chmod 666 /dev/ttyUSB0```

## Compile the examples

1. Examples can be found in the `examples` directory.
    ```
    cd examples
    ```
2. Compile the ***takeoff\_and\_land*** example.
    ```
    cd takeoff_and_land/
    cmake -Bbuild build -H.
    cmake --build build -j4
    ```

## Run the takeoff_and_land example program.

It is recommended to test MAVSDK using the `SIH` simulation. Connect the host to the flight controller’s `TELEM2` port via a TTL-to-USB cable for MAVSDK communication, while also using a Type-C cable to connect the computer and flight controller. Monitor the behavior of the `SIH` simulated aircraft on the ground control station.
<p align="center">
 <img src="figures/mavsdk_connection.png" width="45%">
</p>
Run the demo.

```
cd examples/takeoff_and_land/
build/takeoff_and_land serial:///dev/ttyUSB0:115200
```

> If a connection timeout occurs, simply run the command again.

Next, taking the `takeoff_and_land` example program as an example for study, we will analyze how a complete MAVSDK C++ program is created and run. This will help establish a preliminary understanding of MAVSDK.

This part of the code mainly includes the inclusion of header files and the usage of some namespaces.

```
#include <chrono>  // Include standard library for handling time and durations, mainly used for delays (e.g., sleep_for) and time calculations
#include <cstdint> // Include standard library for fixed-size integer types, such as int32_t, uint8_t, etc.
#include <mavsdk/mavsdk.h> // Include the main MAVSDK library for communication and control of drones
#include <mavsdk/plugins/action/action.h> // Include MAVSDK Action plugin for performing drone actions (e.g., takeoff, land, hover)
#include <mavsdk/plugins/telemetry/telemetry.h> // Include MAVSDK Telemetry plugin to obtain drone telemetry data (e.g., position, attitude, velocity)
#include <iostream>  // Include standard input/output library for printing debug information to the terminal
#include <future>    // Include library for handling asynchronous operations; not used in this example, but typically for multithreading
#include <memory>    // Include smart pointer library for memory management support
#include <thread>    // Include threading library to perform multithreaded operations

using namespace mavsdk;  // Use the mavsdk namespace to simplify references to classes and functions in the MAVSDK library
using std::chrono::seconds;  // Use std::chrono::seconds to represent durations in seconds, simplifying time delay code
using std::this_thread::sleep_for; // Use the sleep_for function to pause the current thread for a specified duration

```
This section of code defines a `usage` function that outputs the program’s usage instructions. When the user runs the program from the command line without providing the correct arguments or needs to see how to use the program, this function is called to display the help information.
```
    void usage(const std::string& bin_name)
    {
        std::cerr << "Usage : " << bin_name << " <connection_url>\n"
                  << "Connection URL format should be :\n"
                  << " For TCP : tcp://[server_host][:server_port]\n"
                  << " For UDP : udp://[bind_host][:bind_port]\n"
                  << " For Serial : serial:///path/to/serial/dev[:baudrate]\n"
                  << "For example, to connect to the simulator use URL: udpin://0.0.0.0:14540\n";
    }
```

Here is the content from the main function:

First, it checks whether the command-line arguments are correct. If they are not, it prints the usage information by calling the `usage` function.
```
    if (argc != 2) {
        usage(argv[0]);
        return 1;
    }
```
Connect to the flight controller.
```
    Mavsdk mavsdk{Mavsdk::Configuration{ComponentType::CompanionComputer}};  
    // Set the MAVLink message sender identity; here, we want the computer to act as an onboard companion computer running MAVSDK to communicate with the flight controller

    ConnectionResult connection_result = mavsdk.add_any_connection(argv[1]); 
    // Add a connection based on the provided connection string/path

    if (connection_result != ConnectionResult::Success) { 
        // Check if the connection was successfully established
        std::cerr << "Connection failed: " << connection_result << '\n';
        return 1;
    }

    auto system = mavsdk.first_autopilot(3.0); 
    // Search and add the first detected autopilot system within 3 seconds

    if (!system) {
        std::cerr << "Timed out waiting for system\n";
        return 1;
    }
```
Initialize plugins,here,the telemetry and action plugins are initialized.
```
    auto telemetry = Telemetry{system.value()};
    auto action = Action{system.value()};
```
Listen to the drone’s altitude information at a frequency of 1 Hz.
```
    const auto set_rate_result = telemetry.set_rate_position(1.0);
    if (set_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting rate failed: " << set_rate_result << '\n';
        return 1;
    }
```
When the altitude information updates, print the altitude data.
```
    telemetry.subscribe_position([](Telemetry::Position position) {
        std::cout << "Altitude: " << position.relative_altitude_m << " m\n";
    });
```
> Before retrieving any telemetry data, you need to subscribe to (listen to) that telemetry information at a certain frequency.

Check whether the drone’s sensor data meets the requirements for arming (unlocking).
```
    while (telemetry.health_all_ok() != true) {
        std::cout << "Vehicle is getting ready to arm\n";
        sleep_for(seconds(1));
    }
```
Send the arm command and determine whether the drone successfully armed based on the returned result.
```
    std::cout << "Arming...\n";
    const Action::Result arm_result = action.arm();

    if (arm_result != Action::Result::Success) {
        std::cerr << "Arming failed: " << arm_result << '\n';
        return 1;
    }
```

Send the takeoff command and determine whether the drone responds to the command to take off based on the returned result.
```
    std::cout << "Taking off...\n";
    const Action::Result takeoff_result = action.takeoff();
    if (takeoff_result != Action::Result::Success) {
        std::cerr << "Takeoff failed: " << takeoff_result << '\n';
        return 1;
    }
```
Wait for ten seconds, then send the land command and monitor whether the drone correctly responds to the landing command. During this period, if the drone completes takeoff, it will enter Hold mode; however, if the waiting time is too short and the drone hasn’t finished taking off, it may still respond to and execute the landing command.
```
    sleep_for(seconds(10));

    std::cout << "Landing...\n";
    const Action::Result land_result = action.land();
    if (land_result != Action::Result::Success) {
        std::cerr << "Land failed: " << land_result << '\n';
        return 1;
    }
```

Check whether the drone is still in the air.
```
   while (telemetry.in_air()) {
        std::cout << "Vehicle is landing...\n";
        sleep_for(seconds(1));
    }
    std::cout << "Landed!\n";
```

After landing, the drone automatically disarms. The example program waits for a short period before exiting.
```
    sleep_for(seconds(3));
    std::cout << "Finished...\n";

    return 0;
```