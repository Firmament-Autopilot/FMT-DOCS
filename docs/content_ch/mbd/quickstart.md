## 下载 FMT-MODEL


要获取FMT-Model，执行以下命令。这将克隆整个FMT-Model的代码库，包括所有可能存在的子模块。
```
git clone https://github.com/Firmament-Autopilot/FMT-Model.git --recursive
```

## 下载MATLAB
FMT-Model主要使用`MATLAB 2018b`进行开发，这是推荐的版本。不过，你也可以使用更高版本的MATLAB。

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
|   |-- FMS
|   |-- INS
|   |-- Plant
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

| Name       | Description                           |
| ---------- | ------------------------------------- |
| bus        | Simulink bus objects and enumeration. |
| lib        | FMT-Model toolbox library.            |
| model      | Algorithm model source file.          |
| script     | Some matlab scripts.                  |
| simulation | Simulation related simulink model.    |
| utils      | some utils.                           |

## FMT-Model 设置

要设置 FMT-Model，请按照以下步骤操作：

1. 在 MATLAB 中打开 FMT-Model所在目录。

   <p align="center">
   	<img src="./figures/model_setup1.png" width="40%">
   </p>


2. 双击打开 `FMT_Model.prj `文件以进行初始化项目。

   <p align="center">
   	<img src="./figures/model_setup2.png" width="40%">
   </p>

3. 默认选择多旋翼模型，要更改选择的模型，访问Simulink Project并打开`Project Path`。将想选择的模型添加到路劲中，并移除不需要的模型。注意修改了模型后，组重新初始化项目。

     <p align="center">
      	<img src="./figures/model_setup3.png" width="40%">
      </p>


4. 打开`Project Path`，确认使用的模型跟你的载具相匹配。比如如下是默认选用多旋翼的算法模型，如果要切换到其它机型比如固定翼，则需要对应多旋翼的模型替换为固定翼的模型。若替换了模型，需关闭 Simulink 项目并清除工作区中的变量。最后，重新运行 FMT_Model.prj 以确保更改生效。

   <p align="center">
   	<img src="./figures/model_setup4.png" width="40%">
   </p>

5. 打开`simulation/MILSIM.slx`模型，查看对应载具的算法模型，包括Plant，INS，FMS和Controller等。

   <p align="center">
   	<img src="./figures/milsim.png" width="40%">
   </p>