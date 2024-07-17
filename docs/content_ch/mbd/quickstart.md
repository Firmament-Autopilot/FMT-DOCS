## 下载 FMT-MODEL


要获取FMT-Model，执行以下命令。这将克隆整个FMT-Model的代码库，包括所有可能存在的子模块。
```
git clone https://github.com/Firmament-Autopilot/FMT-Model.git --recursive
```

## 下载MATLAB
FMT-Model主要使用MATLAB 2018b进行开发，这是推荐的版本。不过，你也可以使用更高版本的MATLAB，不会有任何问题。

以下是所需的工具箱（或更高版本）：

- Aerospace Blockset (4.0)
- Embedded Coder (7.1)
- Instrument Control Toolbox (3.14)
- MATLAB (9.5)
- Simulink (9.2)
- Simulink 3D Animation (8.1)
- Simulink Coder (9.0)

要了解模型所需的其他工具箱信息，请查看模型目录中的README.md文件。该文件将提供关于模型正常运行所需的其他工具箱详细信息。

## 代码目录

以下是FMT-Model源代码目录结构的概述:
```
FMT-Model/
|-- bus/
|-- figures/
|-- lib/
|   |-- FMT_Model_Lib.slx
|-- model/
|   |-- Controller
|   |    |-- Base-Controller
|   |    |-- FW-Controller
|   |-- FMS
|   |    |-- Base-FMS
|   |    |-- FW-FMS
|   |-- INS
|   |    |-- Base-INS
|   |-- Plant
|   |    |-- FW-Plant
|   |    |-- MC-Plant
|-- script/
|-- simulation/
|   |-- DataSIM.slx
|   |-- MILSIM.slx
|-- utils/
|-- FMT_Model.prj
|-- FMT_Model_Init.m
|-- README.md
|-- LICENSE
|-- .gitignore
|-- ...
```

| Name       | Description                            |
| ---------- | -------------------------------------- |
| bus        | Script to create Simulink bus objects. |
| lib        | FMT-Model toolbox model library.       |
| model      | Simulink model source file.            |
| script     | Matlab scripts.                        |
| simulation | Simulation related simulink model.     |
| utils      | Project utils.                         |

## FMT-Model 设置

要设置 FMT-Model，请按照以下步骤操作：

1. 在 MATLAB 中打开 FMT-Model（推荐使用版本为 2018b 或更高版本）。

   <p align="center">
   	<img src="./figures/model_setup1.png" width="40%">
   </p>


2. 双击打开 FMT_Model.prj 文件以打开和初始化项目。

   <p align="center">
   	<img src="./figures/model_setup2.png" width="40%">
   </p>

3. 要选择特定的模型，访问 SIMULINK 项目并导航至项目路径。在这里，您可以查看当前选择的模型（默认情况下选择了多旋翼模型）。

  <p align="center">
   	<img src="./figures/model_setup3.png" width="40%">
   </p>

4. 要切换到固定翼模型，简单地从项目路径中移除多旋翼模型，并添加所需的固定翼模型。在进行这些更改后，关闭 Simulink 项目并清除工作区中的变量。最后，重新运行 FMT_Model.prj 以确保更改生效。

   <p align="center">
   	<img src="./figures/model_setup4.png" width="40%">
   </p>