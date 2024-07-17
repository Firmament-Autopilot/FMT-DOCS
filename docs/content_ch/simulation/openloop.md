
## 数据在环仿真

数据在环仿真是一种在基于模型设计领域分析软件性能的十分有效的方法。*Mlog* 模块记录必要的模型输入数据，其功能类似于黑匣子。将日志解析并喂给仿真模型进行数据在环仿真，我们可以获得模型的输出数据和所有的内部信号。

<p align="center">
 <img src="figures/openloop_diagram.png" width="45%">
</p>


### 记录日志数据。

由于模型从启动开始运行，因此有必要从启动阶段记录日志数据。MLOG_MODE参数决定了何时开始和停止记录日志。

```c
    /* Determines when to start and stop logging.
	0: disabled
	1: when armed until disarm
	2: from boot until disarm
	3: from boot until shutdown  */
    PARAM_INT32(MLOG_MODE, 0),
```

要从启动时开始记录日志，只需在控制台中输入以下命令。请注意，将MLOG_MODE配置为2将会在启动时开始记录日志，并在车辆上锁(Disarm)时停止记录。

```shell
param set MLOG_MODE 2
param save
reboot
```

> 您也可以通过QGC更改MLOG_MODE的值。修改参数值后，请记得输入`param save`保存参数。

如果您打算在单个日志中执行多次测试，例如多次启动和解除系统武装，则应将MLOG_MODE设置为3。此配置将使日志记录继续进行，直到系统关闭或手动停止日志记录。

### 下载日志数据。

日志数据记录在当前会话文件夹中。每次系统启动时，当前会话ID会增加1。当ID达到最大值（通常为20）时，它将重新从1开始，这将覆盖旧的会话文件夹。

要检查当前会话ID，您可以输入boot_log命令。

<p align="center">
	<img src="./figures/session_id.png" width="45%">
</p>

然后，您可以通过QGC的Component->Onboard Files（仅限QGC 3.5.6）或使用SD卡读取器下载日志。

<p align="center">
	<img src="./figures/download_log.png" width="45%">
</p>

### 解析日志数据。

FMT-Model提供了一个Matlab [脚本](https://github.com/Firmament-Autopilot/FMT-Model/blob/master/utils/log_parser/parse_mlog.m)用于解析日志数据。在Matlab中运行该脚本并选择日志文件，脚本将解析日志数据并生成*.mat*文件。

<p align="center">
	<img src="./figures/parsed_log.png" width="45%">
</p>

### 加载日志数据
将所有*.mat*文件拖放到Matlab中。然后运行`load_parameter.m`和`mission_data_preprocess.m`来预处理日志数据。

### 运行数据仿真
现在您可以运行数据仿真。请确保您已通过双击`FMT_Model.prj`初始化了FMT-Model。

打开数据在环仿真模型`simulation/DataSIM.slx`，然后点击**运行**按钮开始仿真。

<p align="center">
	<img src="./figures/data_sim.png" width="75%">
</p>

### 数据检查

当仿真完成后，您可以点击*CompareModelOut*按钮，绘制仿真模型输出数据（红色）与真实数据（蓝色）的对比图。

<p align="center">
	<img src="./figures/compare_model_out.png" width="40%">
</p>

通常情况下，仿真数据应该与实际数据相同。如果不一致，可能存在以下原因：

- 日志数据未从启动时记录。要解决这个问题，您可以将MLOG_MODE设置为2或3，在系统上电时自动开始记录日志。
- 仿真模型与在飞行控制器中运行的模型不同。要解决这个问题，您可以重新构建模型并生成代码。
- 日志中可能存在数据丢失。当日志停止时，您可以检查丢失值是否为0。

<p align="center">
	<img src="./figures/log_lost.png" width="40%">
</p>

### 检查更多数据

您可以使用数据仿真来检索模型的所有数据。您可以选择要检查的数据信号，为其命名并启用数据记录（信号标志将显示）。

<p align="center">
	<img src="./figures/enable_data_log.png" width="50%">
</p>

然后再次运行仿真，您可以使用Simulation Data Inspector（SDI）来可视化数据。

<p align="center">
	<img src="./figures/sdi_signal.png" width="40%">
</p>
