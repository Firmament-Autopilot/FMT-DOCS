# 简介

> 基于模型设计 (Model-Based-Design, MBD) 的下一代开源自驾仪.

## 什么是Firmament?

Firmament (FMT) 是一款基于模型设计 (Model Based Design, MBD) 的开源自驾仪，可被用来快速构建无人机，车，船，机器人等的无人控制系统。基于模型设计已经被广泛应用于汽车制造、航空航天等行业，当前采用基于模型设计模式开发的飞控系统凤毛菱角，而 FMT 就是为此而诞生。

系统主要由两部分构成.

- [FMT-Firmware](https://github.com/Firmament-Autopilot/FMT-Firmware): 一个基于C/C++开发的飞控**嵌入式软件框架**。包含飞控的核心软件，驱动以及功能模块
- [FMT-Model](https://github.com/Firmament-Autopilot/FMT-Model): 一个基于MATLAB/Simulink开发的飞控**算法建模与仿真框架**。包含导航，控制，状态机，被控对象等算法模型。

## 初衷

基于模型设计提供了一个高效的方法来搭建复杂、安全的控制系统。基于模型设计的方法已经被广泛应用于众多行业，主要是汽车制造以及航空航天方面。当前很少有采用基于模型设计模式开发的开源自驾仪系统，而FMT-Autopilot就是为此而诞生。FMT提供了一个理想的平台来测试你最新的算法然后部署到不同的硬件平台或者仅仅运行在仿真器中。FMT提供了强大的仿真能力来帮助您更轻松的调试和优化代码。

## 为什么选择Firmament?

- 基于模型设计提供更高效的开发体验以及更轻松的调试方式 .
- 一个稳定、高性能的基于C/C++设计的嵌入式系统。
- 一个基于MATLAB/Simulink的强大仿真平台，并提供多种算法模块。
- Simulink模型自动代码生成，提升编码效率，减少人为编码错误.
- 出色的实时性能，基于 [RT-Thread](https://www.rt-thread.org/) RTOS, 拥有活跃的开源社区以及大量第三方组件。
- 支持多种开源飞控硬件平台.
- 跨平台的开发工具，支持Windows/Linux/Mac.
- 支持Mavlink协议v1.0/v2.0.
