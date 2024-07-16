
## 软件在环 (SIL) 仿真

软件在环（SIL）仿真是将源代码编译并在主机计算机上运行，为详细的控制策略和系统功能开发与测试提供实用的虚拟仿真环境。

FMT的SIL环境是基于QEMU构建的。通过在QEMU中创建虚拟硬件来让FMT在您的主机PC上运行。

## 设置 SIL

如果您之前没有安装QEMU，您需要首先安装[QEMU (qemu-system-arm)](https://www.qemu.org/download/)。您可以访问QEMU的下载网站。安装完成后，您可以输入以下命令来查看版本信息：

```
qemu-system-arm --version
QEMU emulator version 7.1.94 (v7.2.0-rc4-11947-g2dabd50cfb-dirty)
Copyright (c) 2003-2022 Fabrice Bellard and the QEMU Project developers
```

进入 QEMU 的目标平台目录，并进行编译。

```
cd FMT-Firmware/target/qemu/qemu-vexpress-a9
scons -j4
```

编译完成后，运行 `qemu.sh`(Linux) 或 `qemu.bat`(Windows)。

```
[I/SDIO] SD card capacity 65536 KB.
[I/SDIO] switching card to high speed failed!
can not load /sys/sih_param.xml, use default parameter value.
TOML: fail to open file: /sys/sysconfig.toml

   _____                               __ 
  / __(_)_____ _  ___ ___ _  ___ ___  / /_
 / _// / __/  ' \/ _ `/  ' \/ -_) _ \/ __/
/_/ /_/_/ /_/_/_/\_,_/_/_/_/\__/_//_/\__/ 
Firmware....................FMT FMU v0.2.1
Kernel....................RT-Thread v4.0.3
RAM................................8192 KB
Target....................QEMU vexpress-a9
Vehicle.........................Quadcopter
INS Model..................Base INS v0.1.0
FMS Model..................Base FMS v0.1.0
Control Model.......Base Controller v0.1.0
Plant Model.............Multicopter v0.1.0
Task Initialize:
  local.................................OK
  comm..................................OK
  logger................................OK
  status................................OK
  vehicle...............................OK
[1080] I/StatusTask: SIH Simulation
Hello FMT!

msh />
```

> 对于 Windows 平台，在第一次运行 qemu.bat 脚本后，需要在 FMT 控制台输入 `mkfs sd0` 来格式化 sd.bin 文件。

如果您需要连接QGC，请在运行QEMU命令之前打开它。当QEMU启动并运行时，QGC会自动连接。

<p align="center">
	<img src="./figures/qemu_bootlog.png" width="70%">
</p>

### 多机仿真
SIH仿真可以支持多车辆仿真，无需准备多个飞行控制器硬件。

您可以使用多个Shell进程在每个控制台中运行一个SIL FMT程序。对于每个SIL FMT实例，您需要设置特定的*MAV_SYS_ID*。例如，对于实例1，将MAV_SYS_ID设置为1。对于实例2，将MAV_SYS_ID设置为2，依此类推。

您可以在FMT控制台中使用以下命令设置MAV_SYS_ID，并且需要重新启动SIL FMT才能生效。

```shell
msh />param set MAV_SYS_ID 2
msh />param save
parameter save to /sys/sih_param.xml
```

当MAV_SYS_ID设置正确并且FMT系统正常运行时，您将在QGC中看到多个车辆。

<p align="center">
	<img src="./figures/multi_vehicle.png" width="70%">
</p>