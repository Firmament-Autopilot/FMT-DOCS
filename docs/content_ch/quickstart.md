
## 硬件

当前支持硬件平台 [Pixhawk(FMUv2)](https://docs.px4.io/master/en/flight_controller/pixhawk.html) 和 [Pixhawk 4(FMUv5)](https://docs.px4.io/master/en/flight_controller/pixhawk4.html). 请访问[网站](https://pixhawk.org/)来获得更多硬件信息.

### FMUv2 串口映射

|  UART   | Device  | Port |
|  ----   | ------  | ---- |
|  USART1 | serial6 | IO Debug |
|  USART2 | serial1 | TELEM1 |
|  USART3 | serial0 | TELEM2 |
|  USRT4  | serial2 | GPS |
|  USART6 | serial5 | FMT IO |
|  UART7  | serial4 | SERIAL 4/5 |
|  UART8  | serial3 | SERIAL 4/5 |

### FMUv5 串口映射

|  UART   | Device  | Port |
|  ----   | ------  | ---- |
|  USART1 | serial3 | GPS |
|  USART2 | serial1 | TELEM1 |
|  USART3 | serial2 | TELEM2 |
|  USART6 | serial4 | TELEM3 |
|  UART7  | serial0 | FMU Debug |
|  UART8  | serial5 | FMT IO |

## 工具链

FMT使用如下跨平台的工具链 (Windows/Linux/Mac):

- **编译器**: [arm-none-eabi- toolchain](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads) (version:`7-2018-q2-update`， other version is not well tested).
- **构建工具**: [Scons](https://sourceforge.net/projects/scons/files/scons/2.3.6/) (version：`v2.3.6`, 需要python 2.7.). Python的版本可以在scons脚本的第一行进行指定，比如 `#! /usr/bin/python2`.
- **集成开发环境**: [Visual Studio Code](https://code.visualstudio.com/).
- **USB驱动**: [STM32 USB Driver](https://www.st.com/en/development-tools/stsw-stm32102.html) (仅针对Windows).

## 编译固件

在编译固件之前请确保之前提到的工具链已经正确安装。然后你需要添加一个环境变量`RTT_EXEC_PATH`，将其值设为编译器的路径。

比如对于Linux：

```shell
export RTT_EXEC_PATH=$arm-none-eabi-7-2018-q2-update/bin
```

Pixhawk包含两颗处理器，FMU (Flight Management Unit) 和 IO (Input/Output). 所以我们需要分别编译固件然后依次进行下载。

### 编译FMU固件

首先编译fmu固件。将当前目录切换到对应的目前平台然后输入`scons`指令开始进行编译。生成的fmu附件在`FMT-Firmware/target/pixhawk/fmu-v5/build`目录下。

以编译FMUv5固件为例，使用如下指令：

```shell
cd FMT-Firmware/target/pixhawk/fmu-v5/
scons -j4
```

> `-jN` 可同时执行N个工作，可以编译更快完成。

> 如果编译失败，请先使用`scons -c`来清理工程，并再次尝试编译。

### 编译IO固件

然后我们需要编译io固件. 使用如下指令即可. 生成的io固件位于`FMT-Firmware/target/pixhawk/fmt-io/project/build`目录下.

```shell
cd FMT-Firmware/target/pixhawk/fmt-io/project
scons -j4
```

> 如果编译失败，请先使用`scons -c`来清理工程，并再次尝试编译。

## 下载固件

固件下载需要用到pixhawk的bootloader，请先确保你的pixhawk已经烧写了bootloader。如果没有的话，请访问[PX4-Bootloader](https://github.com/PX4/PX4-Bootloader)来学习如何编译和烧写bootloader到你的硬件。FMT复用pixhawk的bootloader，所以你可以随时将Pixhawk刷回PX4或者APM的固件。

当前fmu固件支持两种下载方式：

- *下载脚本*: 切换当前目录到目标平台然后输入`python uploader.py`. 然后使用usb线将你的硬件连接到PC，下载将自动开始。

```
~/FMT-Firmware/target/pixhawk/fmu-v5$ python3 uploader.py 
waiting for the bootloader...
Auto-detected serial ports are:
Not found fmt_fmu,please connect fmt_fmu!
Not found fmt_fmu,please connect fmt_fmu!
Not found fmt_fmu,please connect fmt_fmu!

Found board id: 50,0 bootloader version: 5 on /dev/serial/by-id/usb-3D_Robotics_PX4_BL_FMU_v5.x_0-if00
sn: 0021001c3438510636363539
chip: 10016451
family: b'STM32F7[6|7]x'
revision: b'Z'
flash: 2064384 bytes
Windowed mode: False

Erase  : [====================] 100.0%
Program: [====================] 100.0%
Verify : [====================] 100.0%
Rebooting. Elapsed Time 6.803

```

> 如果下载失败，请拔掉usb线并重新尝试。

- *QGoundControl*: 进入**Firmware Setup**页面，然后使用usb线将硬件连接到PC，在弹出的串口选择**Advanced Settings**->**Custom firmware file** 以及`fmt_fmu.bin` 固件。

![qgc_download](../figures/qgc_download.png)

如果下载成功，给硬件上电，系统信息将被打印在`serial0`（serial0是默认的控制台设备）。如果你没有串口线，也可以使用QGroundControl的**Mavlink Console**来连接到控制台。

```
   _____                               __ 
  / __(_)_____ _  ___ ___ _  ___ ___  / /_
 / _// / __/  ' \/ _ `/  ' \/ -_) _ \/ __/
/_/ /_/_/ /_/_/_/\_,_/_/_/_/\__/_//_/\__/ 
Firmware....................FMT FMU v0.1.0
Kernel....................RT-Thread v3.0.5
RAM.................................512 KB
Target......................Pixhawk4 FMUv5
Vehicle.........................Quadcopter
INS Model..................Base INS v0.1.0
FMS Model..................Base FMS v0.1.0
Control Model.......Base Controller v0.1.0
Task Initialize:
  comm..................................OK
  logger................................OK
  fmtio.................................OK
  status................................OK
  vehicle...............................OK

msh />
```

下一步是通过fmu来下载io固件。首先将io固件`target/pixhawk/fmt-io/project/build/fmt_io.bin`复制到板载的sd卡上。你可以通过QGroundControl的ftp功能（版本3.5.6，ftp功能在高版本上被移除）或者sd读卡器进行拷贝。然后在fmt的控制台输入如下指令进行io固件下载。

```
msh /usr>fmtio upload /usr/fmt_io.bin
[312785] I/Uploader: sync success
[312793] I/Uploader: found bootloader revision: 5
[312803] I/Uploader: io firmaware:/usr/fmt_io.bin
[312818] I/Uploader: erase...
[314151] I/Uploader: program...
[316275] I/Uploader: CRC check ok, received: 8a27ed4f, expected: 8a27ed4f
```

> 如果是首次下载io固件，在输入`fmtio upload`指令后需要手动按下io的复位按钮，使其进入bootloader。

当下载完成后，输入`fmtio hello`指令，应该可以看到如下从io芯片发来的消息。

```
msh />fmtio hello
msh />[IO]:Hello, this is FMT IO!
```

恭喜！现在你已经成功将FMT运行起来了。:)
