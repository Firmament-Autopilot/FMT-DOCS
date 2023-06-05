
## Software-In-The-Loop (SIL) Simulation

Software-in-the-loop (SIL) simulation is to compile the source code and run it on the host computer to provide a practical, virtual simulation environment for the development and testing of detailed control strategies and system functions.

The SIL environment of FMT is constructed on top of QEMU. By creating a virtual hardware in QEMU to let FMT run on your host PC.

### Setting Up SIL

If you don't have QEMU installed before,  you need install [QEMU (qemu-system-arm)](https://www.qemu.org/download/) first. You can visit QEMU download website. When it is installed properly, you can type following command, which should give you the version information.

```
qemu-system-arm --version
QEMU emulator version 7.1.94 (v7.2.0-rc4-11947-g2dabd50cfb-dirty)
Copyright (c) 2003-2022 Fabrice Bellard and the QEMU Project developers
```

Change cwd to QEMU target and do the compilation.
```
cd FMT-Firmware/target/qemu/qemu-vexpress-a9
scons -j4
```

When compilation is completed, run `qemu.sh` (Linux) or `qemu.bat` (Windows).

```
FMT-Firmware\target\qemu\qemu-vexpress-a9>qemu-system-arm -M vexpress-a9 -kernel build/fmt_qemu-vexpress-a9.bin -display none -device sd-card,drive=sdcard_drive -drive file=sd.bin,format=raw,id=sdcard_drive -serial stdio -serial udp:127.0.0.1:14550@127.0.0.1:14551 -serial udp:127.0.0.1:14552@127.0.0.1:14553
[I/SDIO] SD card capacity 65536 KB.
can not load /sys/sih_param.xml, use default parameter value.
TOML: No config file: /sys/sysconfig.toml, use default configuration.

   _____                               __
  / __(_)_____ _  ___ ___ _  ___ ___  / /_
 / _// / __/  ' \/ _ `/  ' \/ -_) _ \/ __/
/_/ /_/_/ /_/_/_/\_,_/_/_/_/\__/_//_/\__/ 
Firmware.....................FMT FW v0.5.2
Kernel....................RT-Thread v4.0.3
RAM................................8192 KB
Target....................QEMU vexpress-a9
Vehicle........................Multicopter
Airframe.................................1
INS Model..................Base INS v0.3.2
FMS Model..................Base FMS v0.4.0
Control Model.......Base Controller v0.2.4
Plant Model.............Multicopter v0.2.2
Task Initialize:
  offboard..............................OK
  mavobc................................OK
  mavgcs................................OK
  logger................................OK
  status................................OK
  vehicle...............................OK
[1051] I/StatusTask: SIH Simulation
```

> In windows platform, you need format sd.bin by typing `mkfs sd0` command in FMT console when executing the qemu.bat at the first time.

If you need connect QGC, open it before running qemu command. The QGC will be automatically connected when qemu is up and running.

<img src="figures\qemu_bootlog.png" width="50%">

### Multi-Vehicle Simulation
SIH simulation can support multiple-vehicle simulation without the need to prepare multiple flight controller hardware.

You can use multiple shell processes to run a SIL FMT program in each console. For each SIL FMT instance, you need set a specific *MAV_SYS_ID*. For example, for instance 1, set MAV_SYS_ID to 1. For instance 2 set MAV_SYS_ID to 2, etc.

You can use following command in FMT console to set MAV_SYS_ID and it's a reboot of  the SIL FMT is required to take effect.

```shell
msh />param set MAV_SYS_ID 2
msh />param save
parameter save to /sys/sih_param.xml
```

When MAV_SYS_ID is set correctly and FMT system is up running, you will see multiple vehicles in QGC.

<img src="figures\multi_vehicle.png" width="50%">