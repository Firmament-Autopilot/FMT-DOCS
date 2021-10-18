
## Software-In-The-Loop (SIL) Simulation

Software-in-the-loop (SITL or SIL) simulation is to compile the source code and run it on the host computer to provide a practical, virtual simulation environment for the development and testing of detailed control strategies and system functions.

The SIL environment of FMT is constructed on top of QEMU. By creating a virtual hardware in QEMU to let FMT run on your host PC.

## Setting Up SIL

Change cwd to QEMU target and do the compilation.
```
cd FMT-Firmware/target/qemu/qemu-vexpress-a9
scons -j4
```

When compilation is completed, run `qemu.sh` or `qemu.bat`.

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

> In windows platform, you need format sd.bin by typing `mkfs sd0` command in FMT console when executing the qemu.bat at the first time.
