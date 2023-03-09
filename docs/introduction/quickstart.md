
## Download

The FMT code is hosted on [Github](https://github.com/Firmament-Autopilot). Use following git command to download FMT-Firmware. If you don't have [git](https://git-scm.com/downloads) installed, please install it first. 

```
git clone https://github.com/Firmament-Autopilot/FMT-Firmware.git --recursive --shallow-submodules
```

> Note that  FMT-Firmware contains submodules, use *--recursive* to download submodule as well.

## Toolchain

FMT uses the  cross-platform toolchains, therefore can be developed in Windows/Linux/Mac.

### Compiler

FMT-Firmware use [arm-none-eabi- toolchain *7-2018-q2-update*](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads). Do not use other compiler version to avoid unexpected errors and behaviors.

When compiler has been installed, you need create a new enviroment variable `RTT_EXEC_PATH`， which points to the full path of the compiler's bin directory.

**Windows**:

<img src="figures/win_exec_path.png" width="50%">

**Linux**：

Please add the following command at the end of ~/.bashrc, and replace the `<your-compiler-path>` with the compiler's full path.

```
export RTT_EXEC_PATH=<your-compiler-path>/bin
```

### Construction Tool

FMT use SCons as the construction tool. Which is an improved, cross-platform substite for classic Make utility. SCons configuration files are Python scripts, which is easier to use and powerful to solve build problems.

Before installing SCons, you need to make sure that your system has python3 installed. You can go to [the website](https://www.python.org/downloads/) to download python if it's not installed on your system.

Then enter `pip3 install scons` in terminal to install the latest version of SCons. When complete, enter `scons --version` and if version information is printed, that means scons installed successfully.

```
PS C:\Users\zouji> scons --version
SCons by Steven Knight et al.:
        SCons: v4.4.0.fc8d0ec215ee6cba8bc158ad40c099be0b598297, Sat, 30 Jul 2022 14:11:34 -0700, by bdbaddog on M1Dog2021
        SCons path: ['C:\\Users\\zouji\\AppData\\Local\\Programs\\Python\\Python311\\Lib\\site-packages\\SCons']
Copyright (c) 2001 - 2022 The SCons Foundation
```

### IDE

It's recommended to use the Visual Studio Code as the IDE，you can go to [the website](https://code.visualstudio.com) to download it.

To start coding, simply click *File->Open Folder* and select the FMT-Firmware folder.

<img src="figures/vscode.png" width="80%">

> For ease of use, you can install some useful VSCode plugins, such as C/C++, Clang-Format.

## Build

Before compiling the firmware, you need to know the directory structure of FMT-Firmware:

<img src="figures/fmt_dir_structure.png" width="25%">

The main code iis stored in the src directory, and the target directory is the BSP (Board Support Package) for each hardware platform, containing peripheral driver code.

About how to build and download the firmware, it's recommend to refer to the BSP's README:

- [AMOV ICF5](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/amov/icf5/README.md)
- [CUAV V5+](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/cuav/v5_plus/README.md)
- [Pixhawk 4](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v5/README.md)
- [Pixhawk 2.4.6](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v2/README.md)

- [QEMU vexpress-a9](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/qemu/qemu-vexpress-a9/README.md)

Here we take pixhawk4 as am example.

Build fmu firmware for quadcopter

```
cd FMT-Firmware/taget/pixhawk/fmu-v5
scons -j4
```

> -jN allows N jobs at once which makes your build process quicker.
>
> If build fail, please clean the project with command`scons -c` then try to build again.

For other vehicle, such as fixwing, using

```
scons -j4 --vehicle=Fixwing
```

Pixhawk4 has an io (co-processor) processor onboard. Use the following command to build io firmware.

```
cd FMT-Firmware/target/pixhawk/fmt-io/project
scons -j4
```

## Download

### Download FMU Firmware

Currently there are 3 ways to download fmu firmware to hardware.

1. **Donwload Script**: Enter `python3 uploader.py` in the Pixhawk4 directory. Then connect your hardware via usb.

```
PS D:\ws\FMT\FMT-Firmware\target\pixhawk\fmu-v5> python .\uploader.py
waiting for the bootloader...
Error: no serial connection found
wait for connect fmt-fmu...
Error: no serial connection found
wait for connect fmt-fmu...
Error: no serial connection found
wait for connect fmt-fmu...

Found board id: 50,0 bootloader version: 5 on COM3
sn: 0021001c3438510636363539
chip: 10016451
family: b'STM32F7[6|7]x'
revision: b'Z'
flash: 2064384 bytes
Windowed mode: False

Erase  : [====================] 100.0%
Program: [====================] 100.0%
Verify : [====================] 100.0%
Rebooting. Elapsed Time 8.850
```

> If the `"ModuleNotFoundError: No module named 'serial'"` error occurs, indicating that the **pyserial** component is missing, enter `pip3 install pyserial` to install.

1. **QGC**: Go to *Firmware Setup* page，then connect your hardware to PC with a usb cable. In pop-up diaglog，select *Advanced Settings->Custom firmware file* with FMT firmware to download.

   [![qgc_download](https://camo.githubusercontent.com/e41101be77e07f9712a5976764a76ae3aabe8d815111d0e90f2e85745ce4f692/68747470733a2f2f6669726d616d656e742d6175746f70696c6f742e6769746875622e696f2f464d542d444f43532f666967757265732f7167635f646f776e6c6f61642e706e67)](https://camo.githubusercontent.com/e41101be77e07f9712a5976764a76ae3aabe8d815111d0e90f2e85745ce4f692/68747470733a2f2f6669726d616d656e742d6175746f70696c6f742e6769746875622e696f2f464d542d444f43532f666967757265732f7167635f646f776e6c6f61642e706e67)

2. **J-Link**: If you have a JLink, you can connect it to board debug port download the firmware. For more information, please check the [Debug](https://firmament-autopilot.github.io/FMT-DOCS/#/introduction/debug) section.

> Be careful do not override the bootloader!

When system is up and running, the system banner is output via serial0 or you can view it by entering `boot_log` in QGC Mavlink Console.

```
   _____                               __ 
  / __(_)_____ _  ___ ___ _  ___ ___  / /_
 / _// / __/  ' \/ _ `/  ' \/ -_) _ \/ __/
/_/ /_/_/ /_/_/_/\_,_/_/_/_/\__/_//_/\__/ 
Firmware.....................FMT FW v0.3.0
Kernel....................RT-Thread v4.0.3
RAM.................................512 KB
Target......................Pixhawk4 FMUv5
Vehicle.........................Quadcopter
INS Model..................Base INS v0.3.1
FMS Model..................Base FMS v0.4.0
Control Model.......Base Controller v0.2.4
Task Initialize:
  comm..................................OK
  logger................................OK
  fmtio.................................OK
  status................................OK
  vehicle...............................OK
```

### Download IO Firmware

The next step is to upload the io firmware which is downloaded through the fmu. First copy the io firmware `target/pixhawk/fmt-io/project/build/fmt_io.bin` to the on board sd card. You can do that via QGC onboard files page (QGC version 3.5.6 only) or a sd card reader.

[![qgc_download](https://camo.githubusercontent.com/4c7e4352f7205d98467ff143950d073505ee7fa6a4e3c1cf3721399c297d3a2d/68747470733a2f2f71696e69752e6d642e616d6f766c61622e636f6d2f696d672f6d2f3230323330332f32303233303330352f313832303236363233353233333830353834363630393932302e706e67)](https://camo.githubusercontent.com/4c7e4352f7205d98467ff143950d073505ee7fa6a4e3c1cf3721399c297d3a2d/68747470733a2f2f71696e69752e6d642e616d6f766c61622e636f6d2f696d672f6d2f3230323330332f32303233303330352f313832303236363233353233333830353834363630393932302e706e67)

Then enter the following command in FMT console to upload the firmware to io processor.

```
msh /usr>fmtio upload /usr/fmt_io.bin
[312785] I/Uploader: sync success
[312793] I/Uploader: found bootloader revision: 5
[312803] I/Uploader: io firmaware:/usr/fmt_io.bin
[312818] I/Uploader: erase...
[314151] I/Uploader: program...
[316275] I/Uploader: CRC check ok, received: 8a27ed4f, expected: 8a27ed4f
```

> For the first time to download the io firmware, you need click the io reset button on the side after entering fmtio command to let io processor reboot and enter bootloader.

Congratulation! Now you have FMT up and running. :)