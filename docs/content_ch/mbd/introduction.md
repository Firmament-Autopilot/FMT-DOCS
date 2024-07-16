
## Model-Based Design

> “一切可以被控制的对象，都需要被数学量化。”

基于模型的设计（MBD）是一种数学与可视化结合的方法，用于应对设计复杂控制、信号处理和通信系统的挑战。它在多个行业中得到应用，包括运动控制、工业设备、航空航天和汽车。MBD作为一种方法论，用于设计算法模型和生成嵌入式硬件的代码，提供了一种高效且系统化的方式来开发和优化复杂系统的软件模型。

基于模型的设计（MBD）在系统开发的各个阶段（包括建模和仿真）中起着关键作用。它显著有助于遵循V模型开发过程，促进更安全、更可靠系统的创建。通过利用MBD，系统开发过程变得更加顺畅，实现有效的建模和仿真，最终提高最终产品的安全性和可靠性。
<p align="center">
 <img src="figures/v_model.png" width="60%">
</p>
从本质上讲，基于模型的设计（MBD）是一种方法论，通过图形表示和自动代码生成，利用MBD设计软件（如Simulink、MWorks等）创建算法模型。这种方法简化了设计过程，实现了图形模型无缝转换为可执行代码，提升了效率并减少了手动实施的工作。
<p align="center">
 <img src="figures/mbd_software.png" width="100%">
</p>

## 为什么使用MBD

- 提升开发效率：MBD利用图形化编程，使开发人员能够直观地表示复杂系统。这种图形化表示简化了开发过程，使其更高效且易于理解。

- 便捷的维护和模型修改：MBD允许方便地进行模型修改和维护。开发人员可以更轻松地修改和更新模型，模块可以在不同项目中重复使用，促进了代码的可重用性，减少了冗余工作。

- 强大的仿真能力：MBD提供强大的仿真能力，有助于对模型进行彻底的测试和调试。能够仿真各种场景增强了系统优化，并确保更好的性能。

- 自动代码生成：MBD的一个关键优势是自动代码生成。这一特性显著减少了手动编码引起的错误可能性，确保了代码的一致性和可靠性。

- 通过建模标准和检查增强安全性：MBD强制执行严格的建模标准，并进行彻底的模型检查，这有助于提升系统的整体安全性和可靠性。遵循已建立的标准可以最大程度地减少设计缺陷的风险，改善系统的鲁棒性。

<!-- 
## 下载
使用如下指令来下载 FMT-Model 和所有子模块的代码（INS，FMS，Controller，Plant）。
```
git clone https://github.com/Firmament-Autopilot/FMT-Model.git --recursive
```

## 代码目录

FMT-Model 的源码目录如下所示：

| Name          | Description                               |
| ------------- | ----------------------------------------- |
| bus           | Scripts to generate simulink bus objects. |
| figures       | Project figures.                          |
| lib           | FMT-Model toolbox model library.          |
| model         | Simulink model source file.               |
| script        | Matlab scripts.                           |
| simulation    | Simulation related simulink model.        |
| utils         | Project utils.                            | -->
