
## 模型在环 (MIL) 仿真

模型在环是在基于模型设计领早期开发阶段对模型的仿真。 MIL 是一种廉价的算法测试方式。 为了算法能否正常运行，必须在部署到真实硬件之前对其进行仿真，以检测任何可能导致灾难性后果的严重错误。

模型在环仿真是一种在仿真环境（如 Simulink）中开发和运行仿真模型的方法。 一个典型的自驾仪系统模型结构如图所示：

<img src="figures/mil_model.png" width="50%">

## 设置 MIL 仿真

1. 在Matlab (版本2018b或以上) 中打开 FMT-Model。
2. 双击 **FMT_Model.prj** 打开和初始化工程。
3. 打开仿真模型 `simulation/MILSIM.slx`.
4. 选择遥控输入源。右键单击 RC 模块并选择 **Variant->Label Model Active Choice**。

    - Joystick: RC信号由手柄输入。
    - Mavlink: RC信号由硬件通过 mavlink 发送。

5. 打开可视化模块 `MILSIM/Virtualization/3D_Visualization/Matlab_3D/Visualization/VR Sink`。将 viewpoint 设置为 *Isometric - No Rotation*。
6. 点击 **Run** 按钮开始仿真。
7. 仿真完成后，打开 *Simulation Data Inspector* 查看仿真数据。

<img src="figures/mil_sdi.png" width="50%">

## 可视化

Plant 模型生成机体的状态信息，如姿态，位置等。这些信息可以发送给任意的可视化软件，如 Flightgear，Gazebo，Webots等。

<img src="figures/matlab_3D.png" width="40%">
<img src="figures/flightgear.png" width="40%">
