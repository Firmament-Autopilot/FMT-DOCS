## 命令行
FMT 自带许多内置命令，提供了强大的命令系统功能。本节将介绍主要命令的功能和使用方法。要查看系统提供的命令，您可以输入 `help` 命令。
## Command

- Systm Command
    
    [reboot](#reboot)
    
    [ps](#ps)
    
    [free](#free)
    
    [df](#df)
    
    [task](#task)
    
    [exec](#exec)
    
- File System Command

    [ls](#ls)

    [cd](#cd)

    [mkdir](#mkdir)

    [rm](#rm)

    [mv](#mv)

    [cat](#cat)

    [echo](#echo)

    [mkfs](#mkfs)

- Module Command

    [mcn](#mcn)

    [param](#param)

    [mlog](#mlog)

    [act](#act)

    [calib](#calib)

### 系统指令

#### reboot

重启飞控系统

*Usage:* reboot

#### ps

显示系统的线程信息。 

*Usage*: ps

*Example*:  total_cpu_usage=100% - idle_cpu_usage  = 100% - 75.6% = 24.4%

```
msh />ps
thread      pri  status      sp     stack size max used left tick  error cpu
----------- ---  ------- ---------- ----------  ------  ---------- ---   -----
logger       10  suspend 0x0000010c 0x00000800    14%   0x00000001 000   0.08%
mavobc       12  suspend 0x00000134 0x00001000    31%   0x00000001 000   1.17%
vehicle       3  suspend 0x00000128 0x00001400    21%   0x00000001 000   17.71%
status       14  suspend 0x000005b8 0x00001000    36%   0x00000001 000   0.14%
mavgcs       13  suspend 0x00000134 0x00002000    18%   0x00000001 000   5.10%
mav_rx       11  suspend 0x000005ac 0x00002000    45%   0x00000003 000   0.01%
tshell       20  running 0x000002e0 0x00001000    47%   0x00000005 000   0.00%
mtf-01        7  suspend 0x00000130 0x00000800    14%   0x00000001 000   0.15%
wq:hp_work    6  suspend 0x000000dc 0x00001000    05%   0x0000000a 000   0.00%
wq:lp_work   19  suspend 0x000000f8 0x00001000    06%   0x0000000a 000   0.05%
tidle0       31  ready   0x00000068 0x00000400    10%   0x00000014 000   75.60%
timer         5  suspend 0x000000f8 0x00000400    24%   0x0000000a 000   0.00%
```

#### free

显示系统的内存使用情况。总内存表示内存堆栈的整体大小（可使用 malloc 分配的内存大小），已用内存表示当前分配的内存大小，最大分配内存表示曾经分配的最大内存大小。

*Usage*：free
*Example*：

```
msh />free
total memory: 298440
used memory : 68584
maximum allocated memory: 92848
```

#### df

显示存储设备的使用状态。

*Usage*：df [device_path]

- device_path：设备挂载路径。如果挂载了多个存储设备，您可以在 df 命令后添加存储设备的路径，默认路径为根目录 (/)。

*Example*：

```
msh />df
disk free: 14.8 GB [ 31098496 block, 512 bytes per block ]
msh />df /mnt/mtdblk0
disk free: 1.9 MB [ 506 block, 4096 bytes per block ]
```

#### task

系统任务（应用程序）控制命令。它允许对系统任务执行诸如列出、启动、挂起、恢复和终止等操作。

*Usage*：task <cmd>

*cmd*:

- `list`：显示所有任务。
- `start`：启动任务。当任务的 `auto_start=false` 时，使用此命令启动任务。
- `suspend`：挂起任务。
- `resume`：恢复任务。
- `kill`：终止/杀死任务。

*Example*:

```
msh />task list
Topic      Status
--------- ---------
mavobc     RUNNING
mavgcs     RUNNING
logger     RUNNING
status     RUNNING
vehicle    RUNNING
```

#### exec

执行 `.sh` 脚本，该脚本可以包含多条命令。

**usage**：exec <script.sh>

**example**：

```
msh /usr>cat test.sh
version
msh /usr>exec test.sh

 \ | /
- RT -     Thread Operating System
 / | \     4.0.3 build Jul 27 2023
 2006 - 2020 Copyright by rt-thread team
```

--------------------------

### File System Command

#### ls

列出目录的信息。

*Usage*：ls [path]

path：要显示信息的目录路径，如果未提供路径，则默认显示当前目录。

*Example*：

```
msh />ls
sys/  usr/  log/  mnt/
msh />ls sys
param.xml  sih_param.xml  sysconfig.toml  mission.txt
```

#### cd

改变当前工作目录。

*Usage*：cd <path>

path: 输入需要进入的目录。

*Example*:

```
msh />cd sys
msh /sys>
```

#### mkdir

创造一个目录。

*Usage*：mkdir <path>

path: 要创建的目录路径。

*Example*：

```
msh /usr>mkdir test
msh /usr>ls
test/
```

#### rm

删除文件或目录。

*Usage*：

rm <file1> [file2] ...

rm -r <dir1> [dir2] ...

#### mv

从源路径移动或重命名文件或目录到目标路径。

*Usage*：mv <src> <dest>

*Example*：

```
msh /usr>ls
test/
msh /usr>mv test test1
test => test1
msh /usr>ls
test1/
msh /usr>
```

#### cat

显示文件内容。

*Usage*：cat <file>

*Example*：

```
msh />cat /sys/mission.txt
6
0       1       3       22      15.000000       0.000000        0.000000        nan     376278349       -1223895218     8.0000001
1       0       3       16      0.000000        0.000000        0.000000        nan     376285657       -1223885240     8.0000001
2       0       3       16      0.000000        0.000000        0.000000        nan     376269852       -1223883309     8.0000001
3       0       3       16      0.000000        0.000000        0.000000        nan     376270022       -1223905410     8.0000001
4       0       3       16      0.000000        0.000000        0.000000        nan     376283787       -1223907556     8.0000001
5       0       2       20      0.000000        0.000000        0.000000        0.000000        0       0       0.000000        1
```

#### echo

输出字符串到控制台或文件。

*Usage*：echo "string" <file>

*Example*：

```
msh />echo "Hello FMT"
Hello FMT
msh />echo "Hello FMT" test.txt
msh />ls
sys/  usr/  log/  mnt/  test.txt
msh />cat test.txt
Hello FMTmsh />
```

#### mkfs

使用mkfs命令可以格式化磁盘并指定文件系统类型。

*Usage*：mkfs [-t type] <device>

*Example*：

```
mkfs sd0
mkfs -t elm mtdblk0
```

--------------

### Module Command

#### mcn

uMCN是一个内部消息通信模块，`mcn`命令用于执行与uMCN相关的操作命令。

*Usage*：mcn <command> [options]

*cmd*: 

- **list**: 显示所有uMCN消息及相关信息。
- **echo**: 打印特定消息的内容。
  - **-p, --period**: 设置以毫秒为单位的消息打印频率。使用0以发布频率打印消息。
  - **-n, --number**: 设置要打印的消息数量。
  - **-h, --help**: 显示帮助信息。
- **suspend**: 停止发布特定消息。
- **resume**: 恢复发布特定消息。

*Example*:

```
msh />mcn list
Topic                 #SUB   Freq(Hz)   Echo   Suspend
-------------------- ------ ---------- ------ ---------
sensor_imu0_0           0     1000.0    true    false
sensor_imu0             1     1000.0    true    false
sensor_mag0_0           0      100.0    true    false
sensor_mag0             1      100.0    true    false
sensor_baro             1      100.0    true    false
sensor_optflow          1       0.0     true    false
sensor_rangefinder      1       0.0     true    false
external_state          0       0.0     false   false
ins_output              3      500.0    true    false
external_pos            1       0.0     true    false
fms_output              4      250.0    true    false
control_output          2      500.0    true    false
pilot_cmd               3       0.0     true    false
rc_channels             0       0.0     true    false
rc_trim_channels        1       0.0     true    false
gcs_cmd                 2       1.0     true    false
auto_cmd                1       0.0     true    false
mission_data            2       0.0     true    false
bat_status              0       2.0     true    false
msh />mcn echo sensor_imu0 -n 3 -p 200
gyr:-0.001442 0.000879 -0.002189 acc:-0.197859 0.028339 -9.356313
gyr:-0.002067 -0.001984 -0.003562 acc:-0.157055 0.024943 -9.353659
gyr:-0.000076 -0.000723 -0.002266 acc:-0.163536 0.023450 -9.374676
msh />mcn suspend sensor_mag0
msh />mcn resume sensor_mag0
```

#### param

参数操作命令。

*Usage*：param <command> [options]

*cmd*: 

- **list**: 显示参数组及其参数信息。

  - **-a, --all**: 显示所有参数组及其参数信息。

  - **-c, --change**: 仅显示修改过的参数信息。

  - **-d, --default**: 显示默认参数信息。

  - **-h, --help**: 显示帮助信息。

- **set**: 设置参数值。
  - **-h, --help**: 显示帮助信息。

- **get**: 获取参数值。
  - **-h, --help**: 显示帮助信息。

- **save**: 将参数保存到文件中。

- **load**: 加载参数文件。

- **restore**: 将参数恢复为默认值。

  - **-a, --all**: 将所有参数恢复为默认值。

  - **-g, --group**: 将特定参数组的参数恢复为默认值。

  - **-h, --help**: 显示帮助信息。

*Example*:

```
param list

param list CONTROL

param list -a

param set CONTROL ROLL_RATE_P 0.045

param set RATE_I_MIN -- -0.1

param get CONTROL ROLL_RATE_P

param get ROLL_RATE_P

param save

param save /usr/myparam.xml

param load

param load /usr/myparam.xml

param restore CONTROL ROLL_RATE_P

param restore ROLL_RATE_P

param restore -g INS

param restore -g INS CONTROL FMS

param restore --all
```

#### mlog

MLog模块操作命令。

*Usage*：mlog <command> [options]

*cmd*: 

start [file]: 启动 MLog 日志记录（非启动日志）。
stop: 停止日志记录。
status: 显示日志记录状态。

*Example*：

```
msh />mlog start
[3396853] I/MLog: start logging:/log/session_19/mlog1.bin
msh />mlog stop
msh />[3404571] I/MLog: stop logging:/log/session_19/mlog1.bin
[3404571] I/MLog: INS_Out              id:0   record:77       lost:0
[3404571] I/MLog: External_Pos         id:1   record:1        lost:0
[3404571] I/MLog: AirSpeed             id:2   record:1        lost:0
[3404571] I/MLog: OpticalFlow          id:3   record:1        lost:0
[3404571] I/MLog: Rangefinder          id:4   record:1        lost:0
[3404571] I/MLog: GPS_uBlox            id:5   record:1        lost:0
[3404571] I/MLog: Barometer            id:6   record:770      lost:0
[3404571] I/MLog: MAG                  id:7   record:771      lost:0
[3404571] I/MLog: IMU                  id:8   record:3847     lost:0
[3404571] I/MLog: FMS_Out              id:9   record:77       lost:0
[3404571] I/MLog: Mission_Data         id:10  record:1        lost:0
[3404571] I/MLog: Auto_Cmd             id:11  record:1        lost:0
[3404571] I/MLog: GCS_Cmd              id:12  record:9        lost:0
[3404571] I/MLog: Pilot_Cmd            id:13  record:1        lost:0
[3404571] I/MLog: Control_Out          id:14  record:77       lost:0

msh />mlog start /usr/mylog.bin
[3417033] I/MLog: start logging:/usr/mylog.bin
msh />mlog status
log file: /usr/mylog.bin
INS_Out              id:0   record:47       lost:0
External_Pos         id:1   record:1        lost:0
AirSpeed             id:2   record:1        lost:0
OpticalFlow          id:3   record:1        lost:0
Rangefinder          id:4   record:1        lost:0
GPS_uBlox            id:5   record:1        lost:0
Barometer            id:6   record:482      lost:0
MAG                  id:7   record:485      lost:0
IMU                  id:8   record:2423     lost:0
FMS_Out              id:9   record:48       lost:0
Mission_Data         id:10  record:1        lost:0
Auto_Cmd             id:11  record:1        lost:0
GCS_Cmd              id:12  record:6        lost:0
Pilot_Cmd            id:13  record:1        lost:0
Control_Out          id:14  record:49       lost:0
```

#### act

执行器操作命令。

> 注意：如果需要使用该命令操作电机，请先输入 `mcn suspend control_output` 以禁用控制器的输出。否则，执行的命令设置可能会被控制器的输出覆盖。

*Usage*：act <command> [options]

*cmd*: 

- set: 设置执行器输出

  - -d, --device: 要配置的执行器设备名称。

  - -a, --all: 设置所有通道。

  - -c, --channel: 使用十六进制位掩码设置选定的通道。例如，'f' 表示通道 1-4，'5' 表示通道 1 和 3。

  - -h, --help: 显示帮助信息。

- get: 获取执行器输出

  - -d, --device: 要从中获取信息的执行器设备名称。

  - -c, --channel: 使用十六进制位掩码从选定的通道获取信息。例如，'f' 表示通道 1-4，'5' 表示通道 1 和 3。

  - -h, --help: 显示帮助信息。

*Example*：

```
act set -d main_out -a 1200

act set -d main_out -c f 1000 1100 1200 1300

act get -d aux_out -c f
```

#### calib

校准命令，可用于校准遥控器（RC）、空速传感器等设备。

*Usage*：calib <cmd>

*cmd*:

- rc: 校准遥控器（RC）。
- airspeed: 校准空速传感器。

*Example*:

```
msh />calib rc
Before calibration you should move all your sticks to center.
And for safety, please make sure you have disabled all motors!
   o-------o        o-------o
  /         \      /         \
 /           \    /           \
o      ●      o  o      ●      o
 \           /    \           /
  \         /      \         /
   o-------o        o-------o
Next step (y/n)

msh />calib airspeed
diff pressure offset:-93.060173
you need enter param save calibration result
```



