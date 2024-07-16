
## 获取代码
FMT的代码被托管在[Github](https://github.com/Firmament-Autopilot)。请使用如下指令来拉取FMT-Firmware的代码。请确保您有安装[git](https://git-scm.com/downloads)。

```
git clone https://github.com/Firmament-Autopilot/FMT-Firmware.git --recursive --shallow-submodules
```

> 注意因为FMT-Firmware中包含了子模块的代码，使用 *--recursive* 同时拉取子模块。

## 工具链

FMT 利用跨平台工具链，支持在各种操作系统上开发，例如 Windows、Linux 和 Mac。这种灵活性确保了开发人员无论使用何种平台都能自由地进行项目开发。

### 编译器
FMT使用arm-none-eabi- toolchain *7-2018-q2-update*版本，可上其[官网](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads)进行下载安装，在开发过程中，务必要使用指定的编译器版本，以防止出现意外错误或不可预见的行为。不推荐使用其他编译器版本，以保持项目的稳定性和一致性。

编译器下载完成后，下一步是创建一个名为`RTT_EXEC_PATH`的新环境变量，并该变量应指向编译器 bin 目录的完整路径。以下是设置环境变量的方法：


1. 找到编辑器文件夹中包含 `gcc`、`gdb` 等可执行文件的编译器 bin 目录的路径。


2. 将 RTT_EXEC_PATH 环境变量设置为此完整路径。根据您的操作系统，设置过程有所不同：

   - **Windows**:打开控制面板，依次进入“系统和安全 > 系统 > 高级系统设置 > 环境变量”。在“用户变量”下点击“新建”，然后输入变量名为 RTT_EXEC_PATH，变量值为编译器 bin 目录的完整路径。

    <p align="center">
      <img src="./figures/win_exec_path.png" width="40%">
    </p>

   
   - **Linux/Mac**: 打开终端并编辑 shell 配置文件(例如, `~/.bashrc`, `~/.bash_profile`, or `~/.zshrc`, 具体取决于您使用的 shell). 在文件中添加以下一行：
   
    ```bash
     export RTT_EXEC_PATH=/path/to/compiler/bin
    ```
   
   保存文件并运行 source ~/.bashrc（或对应的 shell 配置文件）以使更改生效。

通过设置 `RTT_EXEC_PATH` 环境变量，您可以使系统在开发过程中轻松定位和使用编译器工具。


### 构建工具

FMT 采用 [SCons](https://scons.org/) 作为其构建工具，SCons是传统 Make 工具的增强版,是一个有效的跨平台的替代方案。SCons 配置文件使用 Python 编写，提供了一种用户友好且强大的方法来解决与构建相关的挑战。Python 的灵活性和易用性使配置构建过程更加直观和高效。使用 SCons，开发人员可以享受到一个能够适应不同平台并且简化以及高效的构建系统。


在安装 SCons 之前，确保系统已经安装了 Python 3 是至关重要的。如果您的系统尚未安装 Python 3，您可以从[这里](https://www.python.org/downloads/)下载。Python 是 SCons 的先决条件，因为 SCons 的配置文件是用 Python 脚本编写的。安装完 Python 3 后，您可以继续安装 SCons，以便为项目启用更顺畅和高效的构建过程。

确保您的系统已经安装了Python 3后，在终端中按照以下步骤操作：

1. 要安装最新版本的SCons，请输入以下命令：

   ```bash
   > pip3 install scons
   ```

   > 如果这个命令在您的系统上无法工作，您可以下载 [scons local package](https://scons.org/pages/download.html)。scons-local 包含可以独立执行的 SCons 版本，可以直接从本地目录运行，无需安装。**别忘了将 SCons 路径添加到您的环境变量中**。

2. 安装完成后，您可以通过输入以下命令来检查是否成功安装了 SCons：

   ```bash
   > scons --version
   ```

3. 如果在终端上打印出版本信息，说明 SCons 已成功安装，您现在可以将其作为项目的构建工具使用了。
   
```
PS C:\Users\zouji> scons --version
SCons by Steven Knight et al.:
        SCons: v4.4.0.fc8d0ec215ee6cba8bc158ad40c099be0b598297, Sat, 30 Jul 2022 14:11:34 -0700, by bdbaddog on M1Dog2021
        SCons path: ['C:\\Users\\zouji\\AppData\\Local\\Programs\\Python\\Python311\\Lib\\site-packages\\SCons']
Copyright (c) 2001 - 2022 The SCons Foundation
```

### 集成开发环境

使用 Visual Studio Code (VS Code) 作为集成开发环境（IDE）是强烈推荐的，特别是在使用 FMT 和 SCons 进行工作时。您可以从官方网站下载 [VS Code](https://code.visualstudio.com)。

VS Code 提供了强大且用户友好的开发环境，具备广泛的扩展和功能，非常适合处理像 FMT 这样利用 SCons 作为构建工具的项目。其灵活性、可扩展性以及与多种编程语言的兼容性使其成为开发者们的热门选择。

要在 Visual Studio Code 中开始编写 FMT-Firmware 项目的代码，请按照以下步骤操作：

1. 打开 Visual Studio Code。
2. 在左上角点击 "File" 菜单。
3. 从下拉菜单中选择 "Open Folder"。
4. 将会出现一个文件资源管理器对话框。导航到存储有 FMT-Firmware 文件夹的位置。
5. 选择 FMT-Firmware 文件夹，然后点击 "打开"。

现在，您已经在 Visual Studio Code 中打开了 FMT-Firmware 项目，可以开始使用 IDE 提供的各种功能和扩展来编写、修改和管理项目了。祝编码愉快！
<p align="center">
  <img src="figures/vscode.png" width="60%">
</p>
当然，安装有用的 Visual Studio Code (VS Code) 插件可以显著增强您在处理 FMT-Firmware 项目时的开发体验。以下是两个对 FMT-Firmware 项目至关重要的插件：

1. C/C++：这个插件在 VS Code 中为 C 和 C++ 语言提供了优秀的支持，包括代码高亮、智能感知、代码导航以及专为这些语言定制的调试能力。
2. Clang-Format: Clang-Format 是用于 C、C++ 和其他编程语言的代码格式化工具。在 VS Code 中使用 Clang-Format 插件可以根据特定的风格指南自动格式化您的代码，确保代码一致性和可读性。

要安装这些插件，请按照以下步骤操作：

1. 打开 Visual Studio Code。
2. 点击左侧边栏上的“扩展”图标（或者使用快捷键 Ctrl+Shift+X，macOS 上使用 Cmd+Shift+X）。
3. 在扩展市场搜索栏中输入 “C/C++” 和 “Clang-Format”。
4. 点击每个插件的“安装”按钮。
5. 安装完成后，您可能需要重启 Visual Studio Code 以激活插件。

安装这些插件后，您的开发环境将更好地处理 FMT-Firmware 项目的复杂性，使编码和格式化任务更加高效和便捷。

## 编译固件

在编译固件之前，理解 FMT-Firmware 的目录结构非常重要。以下是您在 FMT-Firmware 项目中通常可以找到的典型目录结构的概述：
<p align="center">
  <img src="figures/fmt_dir_structure.png" width="25%">
</p>


目录和文件的解释：

1. **src/**:这个目录包含固件的主要源代码文件。通常情况下，  `startup.c` 文件是固件的入口点。其他目录，例如 `driver`、`hal`、`lib`、`modules` 等，表示固件的不同组件，使其模块化且具有良好的组织性。

2. **rtos/**:在这个目录中，您会找到与 FMT-Firmware 项目中使用的实时操作系统 (RTOS) 相关的代码。具体来说，这个目录包含了RTOS的实现，本项目使用的是 [rt-thread](https://www.rt-thread.io/) RTOS。

3. **target/**:这个文件夹包含了针对固件目标硬件的板级支持包 (BSP)。BSP 是一个关键组件，用于促进固件与其运行硬件之间的交互。

4. **unit_test/**:`unit_test/` 目录包含单元测试的代码，单元测试在固件中起着至关重要的作用，它可以单独测试每个组件或单元，确保其健壮性和正确性。单元测试对于保持固件的可靠性至关重要。

以上解释提供了对 FMT-Firmware 项目目录结构更清晰的理解，突出了每个目录在固件开发上下文中的角色和内容。

要了解如何构建和下载固件，请查阅每个 BSP 的 README 文件。README 文件通常提供了详细的步骤、依赖项和配置说明，确保固件成功编译和下载。

对于特定硬件的编译，请参考对应BSP目录下的README：

- [AMOV ICF5](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/amov/icf5/README.md)
- [CUAV V5+](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/cuav/v5_plus/README.md)
- [Pixhawk 4](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v5/README.md)
- [Pixhawk 2.4.6](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v2/README.md)
- [QEMU vexpress-a9](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/qemu/qemu-vexpress-a9/README.md)

在以下说明介绍中，我们将以 编译pixhawk/fmu-v5 作为示例，来阐述固件构建过程。

要为多旋翼飞行器构建固件，请按照以下步骤操作：

1. 导航到多旋翼飞行器目标的相关目录：

```
cd FMT-Firmware/taget/pixhawk/fmu-v5
```

2. 使用 scons 命令启动构建过程：

```
scons -j4
```
使用 `-j4` 标志可以启用并行构建，使用 4 个任务同时进行，加快构建过程并减少编译时间。默认情况下，固件是为多旋翼飞行器 (vehicle=Multicopter --airframe=1) 构建的。对于其他车辆和机架类型，请参考[载具/机架](introduction/vehicle_type.md)部分。

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

1. **下载脚本**: 在终端中进入pixhawk/fmu-v5的BSP目录并输入`python3 .\uploader.py`，然后使用USB连接飞控硬件到电脑.

   ```shell
   FMT-Firmware\target\pixhawk\fmu-v5>python .\uploader.py
   waiting for the bootloader...
   Error: no serial connection found
   wait for connect fmt-fmu...
   Error: no serial connection found
   wait for connect fmt-fmu...
   
   Found board id: 50,0 bootloader version: 5 on COM3
   sn: 0027003a5931500620333539
   chip: 10016451
   family: b'STM32F7[6|7]x'
   revision: b'Z'
   flash: 2064384 bytes
   Windowed mode: False
   
   Erase  : [====================] 100.0%
   Program: [====================] 100.0%
   Verify : [====================] 100.0%
   Rebooting. Elapsed Time 10.136
   ```


> 如果出现 `"ModuleNotFoundError: No module named 'serial'"`错误, 说明缺少 **pyserial** 组件, 输入`pip3 install pyserial` 进行安装.

如果脚本无法自动检测到端口，您可以使用 `--port` 选项手动指定端口。例如，您可以使用以下命令显式指定端口：

```
python3 uploader.py --port COM3
```


2. **QGC**: 进入 *Firmware Setup(设置)* 界面，然后使用USB将飞控连接到电脑. 在弹出的对话框中选择 *高级设置->自定义固件文件* ，然后选择FMU的bin固件进行下载。
<p align="center">
   <img src="figures/QGC_download.jpg" width="60%">
</p>
3. **J-Link**: 如果你有JLink，您可以将其插到硬件的debug端口来下载固件。更多的信息，请参考[Debug](https://firmament-autopilot.github.io/FMT-DOCS/#/content_ch/introduction/debug)章节的内容.

> 请小心不要覆盖掉bootloader!

当系统启动并运行时，系统横幅可以通过 `serial0`（串行控制台）显示，或者可以在 QGroundControl（QGC）的 mavlink 控制台中输入 `boot_log` 来查看。这些信息提供了关于系统状态和配置的重要信息，帮助用户监控系统行为，确保系统正常运行。

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

下一步是通过fmu来下载io固件。首先将io固件`target/pixhawk/fmt-io/project/build/fmt_io.bin`拷贝到系统的sd卡上。您可以通过QGC的 *组件-> Onboard Files* 界面来上传（仅QGC3.5.6版本支持）或者使用SD读卡器来实现。 `（注意：本步骤仅针对包含协处理，即IO芯片的硬件平台）`
<p align="center">
  <img src="figures/io_hardware.png" width="50%">
</p>

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
> 编译fmt-io，进入 *targets/pixhawk/fmt-io/projet* 运行编译命令行 `scons -j4` 

> 对于第一次下载io固件，您需要在输入fmtio指令后按下侧边的io复位键来让io处理器重启并进入bootloader。



恭喜！现在你已经成功将FMT运行起来了。:)
