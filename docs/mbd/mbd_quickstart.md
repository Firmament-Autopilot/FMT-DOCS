
## Model-Based Design

> "Everything that can be controlled needs to be mathematically quantified."

Model-based design (MBD) is a mathematical and visual method of addressing problems associated with designing complex control, signal processing and communication systems. It is used in many motion control, industrial equipment, aerospace, and automotive applications. Model-based design is a methodology applied in designing embedded software.

Model-based deisgn plays a very important role in the most stages of system development. Such as Modeling & Simulation. The model-based design makes it easier to comply with V-Model development process which helps create a safer and more reliable system.

<img src="figures/v_model.png" width="40%">

Simply, MBD is a method with algorithm models built by MBD design software (such as Simulink, MWorks, etc.) through graphical and automatic code generation.

**MBD Advantages**：
- Graphical programming enhances development efficiency.
- Maintain and modify models more easily and with greater module reusability.
- Powerful simulation capabilities improve debugging and optimization efficiency.
- Automatic code generation reduces errors caused by manual coding.
- Strict modeling standards and model checks provide enhanced safety.

## Required Toolbox

FMT-Model is developed in MATLAB 2018b, which is the recommend version. However, you should be able to use higher MATLAB version as well. 

The following toolbox (or higher version) are required.

- Aerospace Blockset (4.0)
- Embedded Coder (7.1)
- Instrument Control Toolbox (3.14)
- MATLAB (9.5)
- Simulink (9.2)
- Simulink 3D Animation (8.1)
- Simulink Coder (9.0)

For other toolboxes required by the model, please check the *README.md* inside the model.

## Download
To get the FMT-Model, use the following command. This will clone the code base of FMT-Model with all submodules if any.
```
git clone https://github.com/Firmament-Autopilot/FMT-Model.git --recursive
```

## Code Catalogue

FMT-Model source code catalog is shown as follow:

| Name          | Description                               |
| ------------- | ----------------------------------------- |
| bus           | Scripts to generate simulink bus objects. |
| figures       | Project figures.                          |
| lib           | FMT-Model toolbox model library.          |
| model         | Simulink model source file.               |
| script        | Matlab scripts.                           |
| simulation    | Simulation related simulink model.        |
| utils         | Project utils.                            |

## FMT-Model Set-up
- Open FMT-Model in Matlab (version 2018b recommended, or higher versdion).
- Double click `FMT_Model.prj` to open and initialize the project.
- By default, the FMT-Model will use multicopter models. If you want to switch models, for example, using fixwing models (FW Controller, FW FMS, FW Plant). You can select `SIMULINK PROJECT->Project Path`, remove the multicopter models and add fixwing models. After switching models, close Simulink Project and clear variables in workspace, then re-run FMT_Model.prj again.

<img src="figures/switch_project_path.png" width="80%">