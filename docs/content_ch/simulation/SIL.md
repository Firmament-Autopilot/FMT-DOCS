
## 软件在环 (SIL) 仿真

软件在环 (SITL or SIL) 仿真是将源码进行编译并运行在电脑主机端，为系统功能或控制策略的开发和测试提供实用的虚拟仿真环境。

FMT 的 SIL 环境是基于 QEMU 构建的。通过在QEMU中配置虚拟硬件来让 FMT 运行在主机端。

## 设置 SIL

进入 QEMU 的目标平台目录，并进行编译。
```
cd FMT-Firmware/target/qemu/qemu-vexpress-a9
scons -j4
```

编译完成后，运行 `qemu.sh` 或 `qemu.bat`。

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
