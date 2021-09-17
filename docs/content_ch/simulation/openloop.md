
## 开环仿真

开环仿真是一种在基于模型设计领域分析软件性能的十分有效的方法。*Mlog* 模块记录必要的模型输入数据，其功能类似于黑匣子。将日志解析并喂给仿真模型进行开环仿真，我们可以获得模型的输出数据和所有的内部信号。

<img src="figures/openloop_diagram.png" width="50%">

## 设置开环仿真

1. 使能 Mlog 记录开机日志 (将参数 MLOG_MODE 设置为2或3)。
2. 进行飞行测试。
3. 停止 Mlog 记录并获得日志。
4. 使用 [script](https://github.com/Firmament-Autopilot/FMT-Model/blob/master/utils/log_parser/parse_mlog.m) 脚本解析日志并获得 *.mat* 文件。
5. 打开以及初始化 FMT-Model 工程。
6. 加载 *.mat 文件到 Matlab 中并运行 `load_parameter.m`。
7. 打开仿真模型 `simulation/OpenloopSIM.slx`.
8. 点击 **Run** 按钮开始仿真.

## 数据可视化

你可以使用 Simulation Data Inspector (SDI) 来对整个仿真过程中生成的数据进行可视化。或者你可以使用 matlab 绘制任何想要查看的数据。

这是一个使用 SDI 的示例。可以看到仿真数据（红线）和记录的真实数据（蓝线）完全吻合。

<img src="figures/sdi.png" width="50%">
