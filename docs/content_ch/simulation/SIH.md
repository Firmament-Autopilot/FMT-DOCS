
## 纯硬件仿真 (SIH)

纯硬件仿真 (SIH) 是硬件在环仿真 (HIL) 的一种替代方案。在该配置中，所有的算法都运行在嵌入式硬件平台 - 控制器，导航系统，飞行管理系统以及对象模型。桌面电脑仅用来作为可视化设备。

SIH 相比 HIL 有以下两个优点:

- 它通过避免与计算机的双向连接来确保时间同步。因此，用户不需要使用功能强大的台式计算机。

- 整个仿真运行在 FMT 环境中。开发人员可以更轻松地将他们自己的数学模型整合到仿真器中。例如，他们可以修改空气动力学模型或传感器的噪声水平，甚至可以添加新的要模拟的传感器。

<p align="center">
	<img src="./figures/sih_diag.png" width="45%">
</p>

## 设置 SIH

SIH 仿真可以通过在 *fmtconfig.h* 中添加 `#define FMT_USING_SIH` 来开启。然后重新编译系统。

<p align="center">
	<img src="./figures/using_sih.png" width="45%">
</p>

当系统上电时，系统横幅会输出**Plant Model**信息，这表示SIH仿真模式已激活。然后，您可以连接GCS和RC来操作SIH仿真，就像操作真实飞行器一样。

```
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
Plant Model.............Multicopter v0.1.0
Task Initialize:
  comm..................................OK
  logger................................OK
  fmtio.................................OK
  status................................OK
  vehicle...............................OK
[559] I/StatusTask: SIH Simulation
```

### SIH仿真与多旋翼飞行器

FMT内置了多旋翼飞行器的机型模型，因此您可以使用SIH来进行多旋翼仿真。确保`#define FMT_USING_SIH`未被注释。然后使用以下命令来构建固件。

```
scons -j4
```

>默认车型是多旋翼飞行器，因此您无需显式添`--vehicle=Multicopter`。

通过USB将飞行控制器连接到QGC。您可以使用遥控器（RC）、地面控制站（GCS）或机载计算机来控制飞行控制器，就像控制真实飞行器一样。

### SIH仿真与固定翼飞行器

FMT内置了固定翼飞行器的机型模型，因此您可以使用SIH来进行固定翼仿真。确保`#define FMT_USING_SIH`未被注释。然后使用以下命令来构建固件。

```
scons -j4 --vehicle=Fixwing
```

通过USB将飞行控制器连接到QGC。您可以使用遥控器（RC）、地面控制站（GCS）或机载计算机来控制飞行控制器，就像控制真实飞行器一样。