
## Model-Based Design

> "Everything that can be controlled needs to be mathematically quantified."

<img src="figures/v_model.png" width="40%">

Model-based design (MBD) is a mathematical and visual method of addressing problems associated with designing complex control, signal processing and communication systems. It is used in many motion control, industrial equipment, aerospace, and automotive applications. Model-based design is a methodology applied in designing embedded software.

Model-based deisgn plays a very important role in the most stages of system development. Such as Model & Simulation, SIL, HIL and so on. The model-based design makes it easier to comply with V-Model development process which helps create a safer and more reliable system.

FMT-Model is develped in MATLAB 2018b, which is the recommend version. However, you should be able to use higher MATLAB version as well.

## Download
To get the FMT-Model, use the following command. This will clone the code base of FMT-Model with all submodules (INS, FMS, Controller, Plant).
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
