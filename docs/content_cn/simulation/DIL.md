
#### 数据在环仿真

数据在环仿真（Data-in-loop Simulation）是一种在基于模型设计领域分析数据十分有效的方法，也是FMT所特有的一种仿真形式。通过飞控的日志模块记录模型输入数据，将日志解析后喂给仿真模型进行数据在环仿真，我们可以**获得模型的内部以及输出的所有数据**。

传统的日志记录方式仅能记录需要的数据，因为存储设备带宽所限无法记录所有数据。这样当发生问题时，如坠机，可能因为关键数据没有记录而无法排查，又已经无法或者难以复现问题，导致无法找到问题的根本原因。

通过数据在环仿真就能完美解决这个问题，仅需记录少量的模型输入数据，即可通过数据在环仿真还原出所有数据，并且仿真得到的结果跟真实数据完全一致！

<p align="center">
 <img src="content_cn/simulation/figures/datasim.png" width="80%">
</p>



#### 记录日志

由于模型从系统启动就开始运行，因此有必要从启动阶段就记录日志数据，以保证数据在环仿真的结果跟实机运行的结果完全一致。`MLOG_MODE`参数决定了日志的记录行为，包括何时开始和停止记录日志。我们可以通过将MLOG_MODE设置为2或者3来让飞控从启动后自动开始记录日志，即开机日志。

MLOG_MODE参数含义：

| MLOG_MODE | 含义                                                         |
| --------- | ------------------------------------------------------------ |
| 0         | 日志记录关闭                                                 |
| 1         | 解锁开启日志，上锁关闭日志                                   |
| 2         | 开机后自动开启日志，上锁关闭日志，解锁后再次开启日志         |
| 3         | 开机后自动开启日志，日志将一直持续，直到关机或者手动输入`mlog stop`指令关闭日志记录 |

> MLOG_MODE为2或3都是记录的开机日志，差别是若设置为2，当一次飞行结束飞机上锁后日志就停止了，下次再解锁记录的日志就不是开机日志了

<br>

要修改MLOG_MODE参数值，我们可以在飞控终端使用`param set`指令。注意，参数修改完后需要保存，并且重启飞控后生效。

```shell
param set MLOG_MODE 2
param save
reboot
```

> 您也可以通过QGC更改MLOG_MODE的值。修改参数值后，请记得输入`param save`保存参数。

<br>

日志开始记录后，我们可以在飞控终端输入`mlog status`来查看当前日志的记录情况，包括日志是否正在记录，以及记录的各条日志数量和是否有日志丢包的情况，如下所示：

```
msh />mlog status
log file: /log/session_1/mlog1.bin
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

> 如果记录的不是开机日志或者日志存在丢包情况，那么数据在环仿真的结果将跟真实结果存在差异。

#### 下载日志

日志文件会存储在当前的日志会话文件夹中。每次系统启动时，当前日志会话ID会增加1，当ID达到最大值（默认为20）时，它将重新从1开始，并覆盖旧的会话文件夹。

要获得当前会话ID，您可以输入`boot_log`命令，当前*boot_log.txt*文件所在的目录即为当前的日志绘画目录。

<p align="center">
	<img src="content_cn/simulation/figures/session_id.png" width="30%">
</p>
<br>

在停止日志记录后，可以通过`QGC 3.5.6`地面站来下载日志，具体操作是的进入到*Component->Onboard Files*，点击列出文件，然后选中待下载的日志文件点击下载文件。

<p align="center">
	<img src="content_cn/simulation/figures/download_log.png" width="40%">
</p>

> 若日志较多或者文件较大，可以将SD卡取下使用读卡器进行下载

#### 解析日志

FMT-Model提供了一个Matlab [脚本](https://github.com/Firmament-Autopilot/FMT-Model/blob/master/utils/log_parser/parse_mlog.m)用于解析日志数据。在Matlab中运行该脚本并选择需要解析的日志文件，脚本将解析日志数据并生成*.mat*文件，这些文件可以被导入到Matlab中用于数据在环仿真。

<p align="center">
	<img src="content_cn/simulation/figures/parsed_log.png" width="45%">
</p>

#### 运行数据在环仿真
运行数据在环仿真的一般步骤如下：

1. 在Matlab  2018b（或更高版本） 中打开 FMT-Model所在目录
2. 双击运行 `FMT_Model.prj `以执行工程初始化
3. 从飞控处获得开机日志（*mlog.bin*）文件，并运行`parse_mlog.m`脚本解析日志文件
4. 将获得的所有*.mat文件导入到Matlab工作空间种，然后打开`simulation/DataSIM.slx`数据在环仿真模型
5. 点击`Run`按钮运行仿真，等待仿真完成

<p align="center">
	<img src="content_cn/simulation/figures/data_sim.png" width="50%">
</p>


#### 检查数据一致性

当仿真完成后，可以点击右上角的`CompareModelOut`蓝色按钮，绘制仿真模型输出数据（红色）与真实数据（蓝色）的对比图。

<p align="center">
	<img src="content_cn/simulation/figures/compare_model_out.png" width="50%">
</p>


通常情况下，仿真数据应该与实际数据完全一致。如果不一致，可能存在以下原因：

- 日志数据并非开机日志，您可以将MLOG_MODE设置为2或3，在系统上电时自动开始记录日志。
- 仿真模型与在飞行控制器中运行的模型不同。要解决这个问题，您可以重新编译模型并生成代码，然后将其部署到飞控上。
- 日志中可能存在数据丢失，可以在通过mlog status查看日志的记录情况检查是否存在数据丢失。


#### 查看仿真数据

在数据在环仿真跑完后，可以在`Simulink Data Inspector（SDI）`中检索模型的数据。

<p align="center">
	<img src="content_cn/simulation/figures/sdi_signal.png" width="50%">
</p>

您可以在Simulink模型中单机选择要查看的信号线，为其命名，然后点选`Enable Data Logging`，信号线上将出现一个小的”信号“标志。这表示该信号数据将被记录，那么在下次运行数据在环仿真后，将能在SDI中查找到该数据。

<p align="center">
	<img src="content_cn/simulation/figures/enable_data_log.png" width="50%">
</p>


