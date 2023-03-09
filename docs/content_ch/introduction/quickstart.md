
## 获取代码
FMT的代码被托管在[Github](https://github.com/Firmament-Autopilot)。请使用如下指令来拉取FMT-Firmware的代码。请确保您有安装[git](https://git-scm.com/downloads)。

```
git clone https://github.com/Firmament-Autopilot/FMT-Firmware.git --recursive --shallow-submodules
```

> 注意因为FMT-Firmware中包含了子模块的代码，使用*--recursive*同时拉取子模块。

## 工具链

FMT使用跨平台的工具链，故可以支持在Windows，Linux和Mac系统下开发。

### 编译器
FMT使用arm-none-eabi- toolchain *7-2018-q2-update*版本，可上其[官网](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads)进行下载安装，请勿使用其它版本的编译器。

编译器下载完成后，找到编译器安装的完整路径。然后新建一个环境变量`RTT_EXEC_PATH`，并将其值赋为编译器的bin目录的完整路径。

**Windows**:

<img src="figures/win_exec_path.png" width="50%">

**Linux**：

将如下一行指令添加到~/.bashrc文件中的最后一行，并将<your-compiler-path>替换为编译器的完整路径。

```
export RTT_EXEC_PATH=<your-compiler-path>/bin
```

### 构建工具

FMT-Firmware使用SCons构建工具，其功能类似于Make工具。其使用python语言，故语法更加简单，同时也可以使用python语言进行更多灵活的处理。

在安装SCons之前，需要先确保您的系统已经安装了python3。如果未安装，请上[python官网](https://www.python.org/downloads/)进行下载。

然后在系统控制台输入`pip3 install scons`即可下载安装最新版本的SCons。安装完成后，在控制台输入`scons --version`，如果能出现scons的版本信息，说明安装成功。

```
PS C:\Users\zouji> scons --version
SCons by Steven Knight et al.:
        SCons: v4.4.0.fc8d0ec215ee6cba8bc158ad40c099be0b598297, Sat, 30 Jul 2022 14:11:34 -0700, by bdbaddog on M1Dog2021
        SCons path: ['C:\\Users\\zouji\\AppData\\Local\\Programs\\Python\\Python311\\Lib\\site-packages\\SCons']
Copyright (c) 2001 - 2022 The SCons Foundation
```

### 代码编辑器

FMT-Firmware推荐使用Visual Studio Code作为代码编辑器环境，可以到其[官网](https://code.visualstudio.com)进行下载。

如需打开FMT-Firmware工程，只需在VSCode中点击*File->Open Folder*，然后选中FMT-Firmware固件的根目录即可。

<img src="figures/vscode.png" width="80%">

> 为了使用方便，可以安装一些常用的VSCode插件，推荐C/C++，Clang-Format

## 编译固件

在编译固件之前，需要先了解一下FMT-Firmware的大致目录结构：

<img src="figures/fmt_dir_structure.png" width="25%">

FMT的主要代码是存放在src目录下，target目录则是包含各个硬件平台的BSP（Board Support Package）代码，比如如硬件驱动代码。

对于特定硬件的编译，请参考对应BSP目录下的README：

- [AMOV ICF5](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/amov/icf5/README.md)
- [CUAV V5+](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/cuav/v5_plus/README.md)
- [Pixhawk 4](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v5/README.md)
- [Pixhawk 2.4.6](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v2/README.md)
- [QEMU vexpress-a9](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/qemu/qemu-vexpress-a9/README.md)

这里以Pixhawk 4 为例介绍如何编译和下载固件。

编译四旋翼固件

```
cd FMT-Firmware/taget/pixhawk/fmu-v5
scons -j4
```

> -jN allows N jobs at once which makes your build process quicker.

> If build fail, please clean the project with command `scons -c` then try to build again.

对于编译其它的机型，比如固定翼，使用如下命令

```
scons -j4 --vehicle=Fixwing
```

Pixhawk 4有一个板载协处理器（io处理器）。使用如下指令编译协处理器的固件

```
cd FMT-Firmware/target/pixhawk/fmt-io/project
scons -j4
```

## 下载固件

### 下载FMU固件

当前支持3种方式下载FMU固件。

1. **下载脚本**: 在 pixhawk 4的BSP目录输入`python3 uploader.py`，然后使用USB连接飞控硬件到电脑.

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
Rebooting. Elapsed Time 8.850Copy to clipboardErrorCopied
```

> 如果出现 `"ModuleNotFoundError: No module named 'serial'"`错误, 说明缺少 **pyserial** 组件, 输入`pip3 install pyserial` 进行安装.

2. **QGC**: 进入 *Firmware Setup* 界面，然后使用USB将飞控连接到电脑. 在弹出的对话框中选择 *Advanced Settings->Custom firmware file* ，然后选择FMU的bin固件进行下载。

   [![qgc_download](https://camo.githubusercontent.com/e41101be77e07f9712a5976764a76ae3aabe8d815111d0e90f2e85745ce4f692/68747470733a2f2f6669726d616d656e742d6175746f70696c6f742e6769746875622e696f2f464d542d444f43532f666967757265732f7167635f646f776e6c6f61642e706e67)](https://camo.githubusercontent.com/e41101be77e07f9712a5976764a76ae3aabe8d815111d0e90f2e85745ce4f692/68747470733a2f2f6669726d616d656e742d6175746f70696c6f742e6769746875622e696f2f464d542d444f43532f666967757265732f7167635f646f776e6c6f61642e706e67)

3. **J-Link**: 如果你有JLink，您可以将其插到硬件的debug端口来下载固件。更多的信息，请参考[Debug](https://firmament-autopilot.github.io/FMT-DOCS/#/content_ch/introduction/debug)章节的内容.

> 请小心不要覆盖掉bootloader!

当系统运行起来后，系统输出将在*serial0*打印，或者可以通过在QGC的Mavlink Console界面输入`boot_log`来查看。

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
  vehicle...............................OKCopy to clipboardErrorCopied
```

### 下载IO固件

下一步是通过fmu来下载io固件。首先将io固件`target/pixhawk/fmt-io/project/build/fmt_io.bin`拷贝到系统的sd卡上。您可以通过QGC的 Onboard Files界面来上传（仅QGC3.5.6版本支持）或者使用SD读卡器来实现。 

[![qgc_download](https://camo.githubusercontent.com/4c7e4352f7205d98467ff143950d073505ee7fa6a4e3c1cf3721399c297d3a2d/68747470733a2f2f71696e69752e6d642e616d6f766c61622e636f6d2f696d672f6d2f3230323330332f32303233303330352f313832303236363233353233333830353834363630393932302e706e67)](https://camo.githubusercontent.com/4c7e4352f7205d98467ff143950d073505ee7fa6a4e3c1cf3721399c297d3a2d/68747470733a2f2f71696e69752e6d642e616d6f766c61622e636f6d2f696d672f6d2f3230323330332f32303233303330352f313832303236363233353233333830353834363630393932302e706e67)

在FMT控制台输入如下指令来下载io固件到io处理器中。

```
msh /usr>fmtio upload /usr/fmt_io.bin
[312785] I/Uploader: sync success
[312793] I/Uploader: found bootloader revision: 5
[312803] I/Uploader: io firmaware:/usr/fmt_io.bin
[312818] I/Uploader: erase...
[314151] I/Uploader: program...
[316275] I/Uploader: CRC check ok, received: 8a27ed4f, expected: 8a27ed4fCopy to clipboardErrorCopied
```

> 对于第一次下载io固件，您需要在输入fmtio指令后按下侧边的io复位键来让io处理器重启并进入bootloader。



恭喜！现在你已经成功将FMT运行起来了。:)
