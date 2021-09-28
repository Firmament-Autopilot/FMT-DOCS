
## 日志

日志系统的目的是为用户提供记录信息并在以后检索它的工具。 为使得调试更加简便，FMT日志系统支持三种日志形式。

### 开机日志

Boot log模块记录启动阶段的所有控制台输出，有助于查看系统启动信息。启动日志文件将在每次系统启动时创建并保存到工作日志会话文件夹中。工作日志会话 id 存储在 `/log/session_id`。session id 会在系统每次启动时自加。

```
msh />cat /log/session_5/boot_log.txt

   _____                               __ 
  / __(_)_____ _  ___ ___ _  ___ ___  / /_
 / _// / __/  ' \/ _ `/  ' \/ -_) _ \/ __/
/_/ /_/_/ /_/_/_/\_,_/_/_/_/\__/_//_/\__/ 
Firmware....................FMT FMU v0.1.0
Kernel....................RT-Thread v4.0.3
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
```

### 文本日志

Ulog 是 RT-Thread 提供的一个非常简单易用的组件。 它提供了将文本信息写入各种后端的接口，例如控制台、文件系统等。文本日志可以用来记录一些系统运行时信息。

```
[1708] W/Status: FMS Unknown Mode
[1714] I/Status: FMS Status Disarm
[5479] I/Status: FMS Position Mode
[8268] I/Status: FMS Altitude Hold Mode
[9104] I/Status: FMS Manual Mode
[13034] I/Status: FMS Status Standby
[15005] I/Status: FMS Status Arm
[21673] I/Status: FMS Status Disarm
```

> 文件系统后端默认关闭。你可以通过在*fmtconfig.h*中添加`#define ENABLE_ULOG_FS_BACKEND`来开启。

### 数据日志

Mlog 模块提供了实时记录大量数据的能力，这些数据可以稍后被解析为 *.mat* 文件。

你可以在控制台中输入命令 `mlog start`开始日志记录， 使用命令 `mlog stop` 停止记录。 `mlog status`命令会打印出记录的数据信息，比如记录了什么样的数据，记录了多少条消息。

```
msh />mlog start
[3683] I/MLog: start logging:/log/session_10/mlog1.bin
msh />mlog status
log file: /log/session_10/mlog1.bin
"IMU"                id:1   record:2124     lost:0
"MAG"                id:2   record:427      lost:0
"Barometer"          id:3   record:428      lost:0
"GPS_uBlox"          id:4   record:44       lost:0
"Rangefinder"        id:5   record:1        lost:0
"Optical_Flow"       id:6   record:1        lost:0
"Pilot_Cmd"          id:7   record:1        lost:0
"INS_Out"            id:8   record:44       lost:0
"FMS_Out"            id:9   record:44       lost:0
"Control_Out"        id:10  record:44       lost:0
```

MLog的启动/停止过程也可以通过参数`MLOG_MODE`来控制。 你可以使用 `param` 命令来修改该参数的值。 不要忘记在参数值更改后使用 `param save`。 否则所有未保存的参数值将丢失并在下次启动时使用其默认值。

```c
PARAM_GROUP(SYSTEM)
PARAM_DECLARE_GROUP(SYSTEM) = {
    /* Determines when to start and stop logging.
	0: disabled
	1: when armed until disarm
	2: from boot until disarm
	3: from boot until shutdown  */
    PARAM_DEFINE_INT32(MLOG_MODE, 0),
};
```

[script](https://github.com/Firmament-Autopilot/FMT-Model/blob/master/utils/log_parser/parse_mlog.m) 脚本可以被用来解析日志并生成*.mat*文件。将 *.mat 文件加载到Matlab以进行数据可视化和仿真。
