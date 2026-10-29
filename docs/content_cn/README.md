# 简介

> Firmament Autopilot (FMT) ——基于模型设计 (Model-Based-Design, MBD) 的下一代开源智驾仪.

Firmament (FMT) 是一款基于模型设计 (Model Based Design, MBD) 的开源自驾仪，可被用来快速构建无人机、车辆、船舶、机器人等的无人控制系统。基于模型设计 (MBD) 方法允许高效快速地开发高质量的控制系统，适用于各种无人控制平台，包括航空器、地面车辆、船舶和机器人。基于模型设计已经被广泛应用于汽车制造、航空航天等行业，当前采用基于模型设计模式开发的飞控系统凤毛菱角，而 FMT 就是为此而诞生。借助其直观的图形界面和丰富的预构建模块库，FMT 为用户提供了一个强大的工具集，用于构建和测试复杂的控制算法，以及生成高度优化的代码以部署在嵌入式系统上。此外，FMT 具有高度的灵活性和可定制性，允许用户根据特定的应用需求对系统进行调整，并轻松与其他硬件和软件平台集成。因此，FMT 是研究人员、开发人员和工程师快速有效地构建和测试各种无人平台控制系统的理想选择。

FMT系统是一个全面且先进的无人系统解决方案，主要由三个不同的部分组成：

- [FMT-Firmware](https://github.com/Firmament-Autopilot/FMT-Firmware): 一个基于C/C++开发的飞控**嵌入式软件框架**，旨在为用户提供一个稳定且高性能的平台而设计，用于实时运行控制算法和处理传感器数据。该框架包含飞控的核心软件、驱动以及功能模块，并针对嵌入式系统进行了优化。固件具有极高的灵活性和可定制性，能够轻松修改和适应各种应用需求。
- [FMT-Model](https://github.com/Firmament-Autopilot/FMT-Model): 一个基于MATLAB/Simulink开发的飞控**算法建模与仿真框架**。它提供了一个全面的平台，用于在虚拟环境中开发和测试控制算法，无需实际硬件。FMT-Model 包括多种算法库，使得实现控制、惯性导航系统、引导系统等高级技术变得简单。这个仿真框架具有高度的可定制性，允许用户创建自己的模型和场景，并在不同条件下测试其控制算法的性能。
- **FMT-SIM**: 基于3D引擎实现的模拟仿真环境，提供逼真的渲染效果和丰富的仿真场景，以及相机、激光雷达等多种传感器数据的模拟。

 <p align="center">
   <img src="content_cn/figures/structure_diag.png" width="50%">
 </p>

### 基于模型开发

支持基于模型的设计（Model-Based Design，MBD）方法基于MATLAB/Simulink快速搭建无人平台算法原型，如惯性导航，控制率，飞行管理系统，被控对象模型等。模型可直接生成目标硬件平台的C/C++代码，减少手动编码的繁琐，大大提高开发效率。模型贯穿整个开发周期，从需求规格到设计和验证，直到最终的实现和测试，符合V-Model开发标准，提升系统安全可靠性。

 <p align="center">
   <img src="content_cn/figures/v_model.png" width="60%">
 </p>

### 多平台支持

支持多种无人平台，包含多旋翼（四旋翼，六旋翼，八旋翼等），固定翼，VTOL，无人车，无人船等。同时也可用于其它的异构无人平台，更多无人平台的应用等待你去探索！

 <p align="center">
   <img src="content_cn/figures/support_vehicle.png" width="40%">
 </p>

### 完善的仿真功能

FMT平台支持完善的仿真功能，包括硬件在环仿真（Hardware-in-loop，HIL），软件在环仿真（Software-in-loop，SIL），模型在环仿真（Model-in-loop，MIL），纯硬件仿真（Simulation in hardware，SIH），以及独创的数据在环仿真（Data-in-loop Simulation）。

其中数据在环仿真可以通道少量记录的日志数据还原出算法的所有数据，极大提升了调试和开发效率。

<p align="center">
  <img src="content_cn/figures/mil_sim.png" width="50%">
  <br>
  <span style="color: #666; font-size: 14px;">MIL模型在环仿真</span>
</p>

<p align="center">
  <img src="content_cn/figures/hil_sim.png" width="50%">
  <br>
  <span style="color: #666; font-size: 14px;">HIL硬件在环仿真</span>
</p>
<p align="center">
  <img src="content_cn/figures/datasim.png" width="80%">
  <br>
  <span style="color: #666; font-size: 14px;">数据在环仿真</span>
</p>


### 平台特点

- 基于模型的设计方法实现了高效的开发，并使调试更加容易。
- 一个稳定、高性能的基于C/C++设计的嵌入式系统。
- 一个基于MATLAB/Simulink的强大仿真平台，并提供多种算法模块。
- Simulink模型自动生成代码，适配多种硬件平台（ARM、AMD、Intel 等）。
- 出色的实时性能，基于 [RT-Thread](https://www.rt-thread.org/) RTOS, 拥有活跃的开源社区以及大量第三方组件。
- 支持多种开源飞控硬件平台。具体请参考[FMT-Firmware/target](https://github.com/Firmament-Autopilot/FMT-Firmware/tree/master/target)
- 跨平台的开发工具，支持Windows/Linux/Mac.
- 支持Mavlink协议v1.0/v2.0.
- 支持 ROS1 和 ROS2