
## Model-Based Design

> “一切可以被控制的对象，都需要被数学量化。”

<img src="figures/v_model.png" width="40%">

基于模型设计 (MBD) 是一种解决复杂控制、信号处理和通信系统相关问题的可视化数学方法。它被广泛应用于运动控制、工业设备、航空航天和汽车应用领域。基于模型设计是一种应用于设计嵌入式软件设计的有效方法。

Model-based deisgn plays a very important role in the most stages of system development. Such as Model & Simulation, SIL, HIL and so on. The model-based design makes it easier to comply with V-Model development process which helps create a safer and more reliable system.

基于模型设计在系统开发的大多数阶段都扮演者非常重要的角色。例如建模与仿真、SIL、HIL 等。基于模型设计的方法更容易遵循 V-Model 开发流程，有助于构建更安全、更可靠的系统。

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
| utils         | Project utils.                            |
