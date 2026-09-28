## Download FMT-Model

To obtain the FMT-Model, execute the following command. This will clone the entire code base of FMT-Model, including all submodules if present.

```
git clone https://github.com/Firmament-Autopilot/FMT-Model.git --recursive
```

## Install MATLAB

FMT-Model is primarily developed using MATLAB 2018b, which is the recommended version. Nonetheless, you should also be able to use higher versions of MATLAB without any issues.

The following toolbox (or higher version) are required.

- Aerospace Blockset (4.0)
- Embedded Coder (7.1)
- Instrument Control Toolbox (3.14)
- MATLAB (9.5)
- Simulink (9.2)
- Simulink 3D Animation (8.1)
- Simulink Coder (9.0)

To find information about other toolboxes required by the model, kindly refer to the README.md file located within the model's directory. It will provide comprehensive details regarding additional toolboxes necessary for the model to function properly.

## Code Catalogue

Below is an overview of the FMT-Model source code directory structure:

```
FMT-Model/
|-- bus/
|-- figures/
|-- lib/
|   |-- FMT_Model_Lib.slx
|-- model/
|   |-- Controller
|   |	|-- Base-Controller
|   |	|-- FW-Controller
|   |-- FMS
|   |	|-- Base-FMS
|   |	|-- FW-FMS
|   |-- INS
|   |	|-- Base-INS
|   |-- Plant
|   |	|-- FW-Plant
|   |	|-- MC-Plant
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

## FMT-Model Set-up
To set up the FMT-Model, please follow these steps:

1. Open FMT-Model in MATLAB (version 2018b recommended, or higher versdion).

   <p align="center">
   	<img src="./figures/model_setup1.png" width="60%">
   </p>

2. Double click **FMT_Model.prj** to open and initialize the project.

   <p align="center">
   	<img src="./figures/model_setup2.png" width="60%">
   </p>

3. To choose specific models, access the **SIMULINK PROJECT** and navigate to **Project Path**. Here, you can view the currently selected models (By default, the multi-copter models are chosen).

   <p align="center">
   	<img src="./figures/model_setup3.png" width="60%">
   </p>

4. To switch to fixed-wing models, simply remove the multi-copter models from the project path and add the desired fixed-wing models instead. After making this change, close the Simulink Project and clear the variables in the workspace. Finally, re-run the **FMT_Model.prj** to ensure that the changes take effect.

   <p align="center">
   	<img src="./figures/model_setup4.png" width="60%">
   </p>