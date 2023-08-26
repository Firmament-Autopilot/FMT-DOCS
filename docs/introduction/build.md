## Directory Structure

Understanding the directory structure of FMT-Firmware is crucial before compiling the firmware. Below is a general overview of the typical directory structure you can expect to find in the FMT-Firmware project:

```
FMT-Firmware/
|-- rtos/
|   |-- rt-thread
|-- src/
|   |-- driver
|   |-- hal
|   |-- lib
|   |-- model
|   |-- module
|   |-- protocol
|   |-- task
|   |-- startup.c
|   |-- ...
|-- target/
|   |-- amov
|   |-- coolfly
|   |-- cuav
|   |-- cubepilot
|   |-- minimum
|   |-- pixhawk
|   |-- qemu
|-- unit_test/
|   |-- test_case1.c
|   |-- ...
|-- .clang-format
|-- README.md
|-- LICENSE
|-- .gitignore
|-- ...

```

Explanation of directories and files:

1. **src/**: This directory holds the primary source code files of the firmware. The `startup.c` file is typically the entry point of the firmware. Other directories, such as `driver`, `hal`, `lib`, `modules`, etc., represent distinct components of the firmware, making it modular and organized.
2. **rtos/**: Within this directory, you'll find the code related to the Real-Time Operating System (RTOS) utilized in the FMT-Firmware project. It specifically contains the RTOS implementation, and in this case, it utilizes the [rt-thread](https://www.rt-thread.io/) RTOS.
3. **target/**: This folder accommodates the Board Support Package (BSP) specific to the hardware targeted for the firmware. The BSP is a crucial component that facilitates interaction between the firmware and the hardware it runs on.
4. **unit_test/**: The `unit_test/` directory comprises code for unit tests, which play a vital role in testing individual components or units of the firmware in isolation, ensuring robustness and correctness. Unit tests are essential for maintaining the reliability of the firmware.

This enhanced explanation provides a clearer understanding of the directory structure of the FMT-Firmware project, highlighting the role and content of each directory within the context of firmware development.


## Build

To learn how to build and download the firmware, kindly consult the README file of the each BSP. For this explanation, we will use ICF5 as an example to illustrate the firmware building process. The README file typically provides detailed instructions on the necessary steps, dependencies, and configurations required for a successful firmware compilation and download.

- [AMOV ICF5](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/amov/icf5/README.md)
- [CUAV V5+](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/cuav/v5_plus/README.md)
- [Pixhawk 4](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v5/README.md)
- [Pixhawk 2.4.6](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v2/README.md)
- [QEMU vexpress-a9](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/qemu/qemu-vexpress-a9/README.md)

### Build Firmware

To build the firmware for a quadcopter, follow these steps:

1. Navigate to the relevant directory for the quadcopter target:

```
cd FMT-Firmware/target/amov/icf5
```

2. Initiate the build process with the `scons` command:

```
scons -j4
```

The `-j4` flag enables parallel building with 4 jobs, accelerating the build process and reducing compilation time. By default, the firmware is built for the Quadcopter x vehicle (`vehicle=Multicopter --airframe=1`). For other vehicle and airframe types, please refer to the [Vehicle/Airframe](introduction/vehicle_type.md) section.

### Download Firmware

There are two methods available for downloading firmware to the hardware:

1. **Download Script**: To use the download script, navigate to the `icf5` directory and execute the command `python3 uploader.py`. Ensure that your hardware is connected via USB.

   ```shell
   FMT-Firmware\target\amov\icf5> python .\uploader.py
   waiting for the bootloader...
   Error: no serial connection found
   wait for connect fmt-fmu...
   Error: no serial connection found
   wait for connect fmt-fmu...
   
   Found board id: 50,0 bootloader version: 5 on COM7
   sn: 000000000000000000000000
   chip: 22020419
   family: b'GD32F4[5|7]x'
   revision: b'?'
   flash: 1015808 bytes
   Windowed mode: False
   
   Erase  : [====================] 100.0%
   Program: [====================] 100.0%
   Verify : [====================] 100.0%
   Rebooting. Elapsed Time 9.687
   ```

   > If you encounter a `"ModuleNotFoundError: No module named 'serial'"` error, it indicates that the **pyserial** component is missing. You can install it by running `pip3 install pyserial`.

If the script is unable to automatically detect the port, you have the option to specify the port manually using the `--port` option. For example, you can use the following command to indicate the port explicitly:

```
python3 uploader.py --port COM3
```



2. **J-Link**: If you have a JLink, you can connect it to the board's debug port to download the firmware. For more detailed information, please refer to the [Debug](https://firmament-autopilot.github.io/FMT-DOCS/#/introduction/debug) section in the documentation.

   > Take caution not to overwrite the bootloader during the firmware download process.

These two methods provide different options for downloading the firmware onto the hardware, offering flexibility and ease of use to suit your specific requirements.

When the system is up and running, the system banner is displayed either via `serial0` (serial console) or can be viewed by entering `boot_log` in the QGroundControl (QGC) mavlink console. This banner provides important information about the system's status and configurations, allowing users to monitor the system's behavior and ensure it is functioning correctly.

```
-----------FMT Bootloader v1.x-----------
Board Type:         50
Board Revision:     0
Board Flash Size:   1015808
App Load Address:   0x8010000

   _____                               __
  / __(_)_____ _  ___ ___ _  ___ ___  / /_
 / _// / __/  ' \/ _ `/  ' \/ -_) _ \/ __/
/_/ /_/_/ /_/_/_/\_,_/_/_/_/\__/_//_/\__/
Firmware.....................FMT FW v0.5.5
Kernel....................RT-Thread v4.0.3
RAM.................................448 KB
Target...........................Amov-ICF5
Vehicle........................Multicopter
Airframe.................................1
INS Model..................Base INS v0.3.2
FMS Model..................Base FMS v0.4.0
Control Model.......Base Controller v0.2.4
Task Initialize:
  mavobc................................OK
  mavgcs................................OK
  logger................................OK
  status................................OK
  vehicle...............................OK

```

