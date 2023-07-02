
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

About how to build and download the firmware, please refer to the BSP's README:

- [AMOV ICF5](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/amov/icf5/README.md)
- [CUAV V5+](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/cuav/v5_plus/README.md)
- [Pixhawk 4](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v5/README.md)
- [Pixhawk 2.4.6](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v2/README.md)

- [QEMU vexpress-a9](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/qemu/qemu-vexpress-a9/README.md)

