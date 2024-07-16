
## 模型在环 (MIL) 仿真

模型在环是在基于模型设计领早期开发阶段对模型的仿真。 MIL 是一种廉价的算法测试方式。 为了算法能否正常运行，必须在部署到真实硬件之前对其进行仿真，以检测任何可能导致灾难性后果的严重错误。

模型在环仿真是一种在仿真环境（如 Simulink）中开发和运行仿真模型的方法。 一个典型的自驾仪系统模型结构如图所示：
<p align="center">
 <img src="figures/mil_model.png" width="50%">
</p>

### 设置 MIL 仿真

1. 在Matlab (版本2018b或以上) 中打开 FMT-Model。
2. 双击 **FMT_Model.prj** 打开和初始化工程。
3. 打开仿真模型 `simulation/MILSIM.slx`.
<p align="center">
   	<img src="./figures/milsim.png" width="50%">
</p>


### RC Input
遥控（Remote Control）输入可以由`MILSIM/FCS/RC`模块生成。目前可以选择四种选项。要更改活动选项，右键单击空白区域，然后选择*Variant->Label Model Active Choice*。

- **摇杆**：通过插入摇杆发送遥控信号，例如带有USB线的游戏手柄。
- **Mavlink**：遥控信号通过飞行控制器通过mavlink协议发送。因此，您需要将遥控接收器连接到飞行控制器，并将其连接到计算机。确保`fmtconfig.h`中的`#define FMT_OUTPUT_PILOT_CMD`宏未被注释，并且您的飞行控制器可以正确接收遥控信号。
- **遥控器模拟信号**：遥控信号由Simulink滑块模块构建。可以模拟一个假的遥控信号。
- **None**：用于构建没有遥控（遥控断开连接）的情况。因此，您可以使用其他输入源，例如地面站或机载计算机来控制车辆。

### GCS Input
地面控制站（GCS）输入可以由`MILSIM/FCS/GCS`模块生成。您可以使用GCS发送指令来控制车辆。

您还可以通过GCS设置模式。由于遥控（RC）具有比GCS更高的优先级，因此您需要使遥控无效（选择无），然后才能通过GCS设置模式。

### Auto Command
自动命令用于向FMS发送外部控制指令。只有在Offboard模式激活时，自动命令才有效。

### Mission Data
任务数据用于向FMS发送航点数据。任务数据符合mavlink任务协议。更多信息请参考相关资料 [Mission Protocol · MAVLink Developer Guide](https://mavlink.io/en/services/mission.html).

### Visualization

默认的可视化工具是MATLAB 3D。双击Simulink可视化模块`MILSIM/Virtualization/3D_Visualization/Matlab_3D/Visualization/VR Sink`。将视点设置为*Isometric - No Rotation*。

<p align="center">
	<img src="./figures/milvisualize.jpg" width="45%">
</p>

<p align="center">
	<img src="./figures/matlab_3D.png" width="45%">
</p>

对象模型生成车辆状态，例如姿态、位置等。这些信息可以发送到任何可视化软件，如Flightgear、Gazebo、Webots等。

<p align="center">
	<img src="./figures/flightgear.png" width="645">
</p>

### Run Simulation
有多种方法可以运行MIL仿真，您可以像控制真实车辆一样进行控制。这里我们只介绍一种执行*Mission*模式仿真的方法。

1. 将RC的活动选择设置为*无*。
2. 将GCS模式设置为*PilotMode.Mission*。
3. 点击运行按钮开始仿真。
4. 将GCS的cmd_1从*FMS_Cmd.None*更改为*FMS_Cmd.PreArm*（当cmd_1被激活时）。

### Data Analyze
当仿真完成后，您可以通过*Simulation Data Inspector*分析仿真数据来检查仿真结果。
<p align="center">
	<img src="./figures/mil_sdi.png" width="40%">
</p>