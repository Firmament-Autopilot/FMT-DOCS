## Command

FMT comes with many built-in commands, providing powerful command system functionalities. This section will introduce the functions and usage of the main commands. To see which commands the system offers, you can input the `help` command.

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

### System Command

#### reboot

Restart the flight control system.

*Usage:* reboot

#### ps

Display the thread information of the system. 

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

Display the system memory usage. Total memory indicates the overall size of the memory stack (the size of memory available for allocation using malloc), used memory represents the currently allocated memory size, and maximum allocated memory indicates the largest memory size ever allocated.

*Usage*：free
*Example*：

```
msh />free
total memory: 298440
used memory : 68584
maximum allocated memory: 92848
```

#### df

Display the usage status of the storage devices.

*Usage*：df [device_path]

- device_path: Device mount path. If multiple storage devices are mounted, you can add the path of the storage device after df, with the default being the root directory (/) path.

*Example*：

```
msh />df
disk free: 14.8 GB [ 31098496 block, 512 bytes per block ]
msh />df /mnt/mtdblk0
disk free: 1.9 MB [ 506 block, 4096 bytes per block ]
```

#### task

System task (application) control commands. It allows performing operations such as list, start, suspend, resume, and kill on the system's tasks.

*Usage*：task <cmd>

*cmd*:

- list: Display all tasks.
- start <task_name>: Start a task. Use this command to start a task when auto_start=false for that task.
- suspend <task_name>: Suspend a task.
- resume <task_name>: Resume a task.
- kill <task_name>: Terminate/kill a task.

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

Execute .sh script, which can contains multiple commands.

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

List information about the directory.

*Usage*：ls [path]

path: The directory path to display , if no path is provided, the current directory will be shown by default.

*Example*：

```
msh />ls
sys/  usr/  log/  mnt/
msh />ls sys
param.xml  sih_param.xml  sysconfig.toml  mission.txt
```

#### cd

Change the working directory.

*Usage*：cd <path>

path: Directory path to enter.

*Example*:

```
msh />cd sys
msh /sys>
```

#### mkdir

Make a directory.

*Usage*：mkdir <path>

path: Directory path to create.

*Example*：

```
msh /usr>mkdir test
msh /usr>ls
test/
```

#### rm

Remove the files/directory.

*Usage*：

rm <file1> [file2] ...

rm -r <dir1> [dir2] ...

#### mv

Move or rename file/directory from source to destination.

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

Display file contents.

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

Output string to console or file。

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

Format disk with file system.

*Usage*：mkfs [-t type] <device>

*Example*：

```
mkfs sd0
mkfs -t elm mtdblk0
```

--------------

### Module Command

#### mcn

uMCN is an internal message communication module, and the mcn command is used to implement operation commands related to uMCN.

*Usage*：mcn <command> [options]

*cmd*: 

- list: Displays all uMCN messages and related information.
- echo: Prints the content of a specific message.
  - -p, --period: Sets the frequency of message printing in milliseconds. Use 0 to print messages at the publishing frequency.
  - -n, --number: Sets the number of messages to be printed.
  - -h, --help: Displays help information.
- suspend: Stops the publishing of a specific message.
- resume: Resumes the publishing of a specific message.

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

Parameters operation command.

*Usage*：param <command> [options]

*cmd*: 

- list: Display parameter groups and their parameter information.

  - -a, --all: Show all parameter groups and their parameter information.

  - -c, --change: Show only modified parameter information.

  - -d, --default: Show default parameter information.

  - -h, --help: Display help information.

- set: Set parameter values.
  - -h, --help: Display help information.

- get: Get parameter values.
  - -h, --help: Display help information.

- save: Save parameters to a file.

- load: Load parameter file.

- restore: Restore parameters to default values.

  - -a, --all: Restore all parameters to default values.

  - -g, --group: Restore parameters of a specific parameter group to default values.

  - -h, --help: Display help information.

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

MLog module operation command。

*Usage*：mlog <command> [options]

*cmd*: 

- start [file]: Start mlog logging (non-boot logs).
- stop: Stop logging.
- status: Display logging status.

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

Actuator operation command。

> Note: If you need to use this command to operate the motor, please enter `mcn suspend control_output` first to disable the controller's output. Otherwise, the settings made by the act command will be overridden by the controller's output.

*Usage*：act <command> [options]

*cmd*: 

- set: Set actuator output

  - -d, --device: Name of the actuator device to be configured

  - -a, --all: Set all channels

  - -c, --channel: Set selected channels using hexadecimal bitmask. For example, 'f' represents channels 1-4, '5' represents channels 1 and 3.

  - -h, --help: Display help information

- get: Het actuator output

  - -d, --device: Name of the actuator device to retrieve information from

  - -c, --channel: Get information from selected channels using hexadecimal bitmask. For example, 'f' represents channels 1-4, '5' represents channels 1 and 3.

  - -h, --help: Display help information

*Example*：

```
act set -d main_out -a 1200

act set -d main_out -c f 1000 1100 1200 1300

act get -d aux_out -c f
```

#### calib

Calibration command, can be used to calibrate RC, airspeed sensor and so on.

*Usage*：calib <cmd>

*cmd*:

- rc: Calibrate RC.
- airspeed: Calibrate airspeed sensor.

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



