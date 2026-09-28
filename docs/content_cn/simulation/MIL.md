
#### 模型在环 (MIL) 仿真

模型在环是在基于模型设计早期开发阶段对模型的仿真和测试，是一种高效和便捷的算法早期测试方式。

算法在设计之初往往隐含各种Bug， 必须在部署到真机之前对其进行仿真，以检测任何可能导致灾难性后果的严重错误。

模型在环仿真是在 Simulink环境中开发和搭建完整的算法模型，并进行闭环仿真，其特点是算法完全运行在Simulink环境中，而不依赖飞控硬件设备以及嵌入式软件系统。

典型的模型在环仿真结构图如下所示：

<p align="center">
  <img src="figures/mil_model.png" width="40%">
  <br>
  <span style="color: #666; font-size: 14px;">MIL仿真框图</span>
</p>



<p align="center">
  <img src="./figures/milsim.png" width="40%">
  <br>
  <span style="color: #666; font-size: 14px;">MIL Simulink模型</span>
</p>



####  MIL仿真步骤

MIL仿真遵循以下步骤：

1. 在Matlab  2018b（或更高版本） 中打开 FMT-Model所在目录
2. 双击运行 `FMT_Model.prj `以执行工程初始化
3. 打开仿真模型 `simulatSion/MILSIM.slx`，并点击`Run`按钮运行仿真
3. 打开Virtualization可视化模块，根据当前所选的模块，打开对应可视化软件
3. 通过RC或者GCS模块设置飞行模式，如`PilotMode.Position`，然后将cmd_1（变化有效）设置为`FMS_Cmd.Takeoff`，让飞机起飞。

<p align="center">
  <img src="./figures/mil_sim.png" width="40%">
  <br>
  <span style="color: #666; font-size: 14px;">MIL模型在环仿真</span>
</p>

#### 数据分析

当仿真完成后，您可以通过Simulink自带的*Simulation Data Inspector*数据分析工具查看任意的仿真数据。
<p align="left">
	<img src="./figures/mil_sdi.png" width="50%">
</p>

#### 其它模块

你可能已经注意到了，在MILSIM中除了Plant，INS，FMS和Controller这四个算法模型外，还有一些其它的模块，他们主要是构造模型在环仿真所需要的输入/输出数据，下面是各个模块功能的介绍：

##### RC模块

RC（Remote Control）遥控模块用来构建和模拟遥控输出数据。目前支持选择四种选项，如下所示。要更改所选项，右键单击空白区域，然后选择*Variant->Label Model Active Choice*。

- **Joystick**：通过USB接入手柄设备发送遥控信号，例如游戏手柄。
- **Mavlink**：飞控经由mavlink协议发送遥控信号。此方式需修改飞控的`fmtconfig.h`文件，打开`#define FMT_OUTPUT_PILOT_CMD`宏开关，并且确保飞控已经连接遥控并可以正确接收遥控信号。
- **Virtual**：由Simulink的Slider模块构建的虚拟遥控。
- **None**：用来模拟遥控器未连接的情况。这很有用，因为遥控器失效的情况下才能通过地面站模块（GCS）来设置飞行模式，因为遥控的模式相比地面站拥有更高优先级。

<p align="left">
	<img src="./figures/rc_block.png" width="20%">
</p>

##### GCS模块
地面控制模块（GCS）用来模拟地面站的指令，如设置飞行模式，以及触发起飞、降落、返航等指令。

> 注意，遥控相比于地面站拥有更高的模式优先级，所以只有当遥控模块无效（设置为None）的时候，地面站模块的模式设置才会生效

<p align="left">
	<img src="./figures/gcs_block.png" width="30%">
</p>

##### Auto_Cmd模块

该模块用来模拟像FMS飞行管理模型发送自动控制指令以实现任意运动控制或轨迹控制。该命令只有在Offboard外部控制模式激活时才有效。

##### Mission_Data模块

该模块用于向FMS飞行管理模型发送航点数据，以实现航点飞行控制。任务数据以mavlink的mission协议标准进行构建，更多信息请参考相关资料 [Mission Protocol · MAVLink Developer Guide](https://mavlink.io/en/services/mission.html)。

##### Visualization模块

该模块是可视化模块，用于将模型在环仿真的位置、姿态等状态信息发送给外部的视景软件系统以实现可视化功能。

可视化模块当前支持3种外部视景软件，分别是**Matlab-3D**, **Flightgear**或者**UE4**，默认的可视化工具是UE4, 需搭配FMT-SIM来使用；Matlab-3D是Simulink自带的3D显示模块，可以双击进行打开。

<p align="left">
	<img src="./figures/milvisualize.png" width="35%">
</p>