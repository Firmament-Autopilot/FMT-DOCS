
## Add New Model

FMT Model provides a framework which can easily develop new model, or re-developement based on the existing model. We use an example here to demonstrate how to add a new controller model.

### Create New Model

To add a new controller model into FMT Model, please follow the steps below.

- Copy an existing controller model folder and give it a new name, e.g. `Fixwing_Controller/`.
- The folder contains the following files.
  - **Controller.slx**: Controller simulink model file.
  - **controller_model_init.m**: Model initialization script, which is executed by FMT Model.
  - **LICENSE**: The license file of your model.
  - **README.md**: The model introduction.

<img src="figures/template_folder.png" width="50%">

- Modify the content of `controller_model_init.m` to fit your model.

The content of an example controller_model_init.m is shown below. The model execution frequency is 500Hz, which can be changed by modifying the `CONTROL_CONST.dt`. You can also add parameters for your model, which will be exported to FMT Firmware as well. So these parameters are accessible in the embedded layer.

```matlab
% 
% This is a template controller model
% Firmament Autopilot
%
controller_version = 'v0.0.1';  % model version

%% Constant Variable
CONTROL_CONST.dt = 0.002;   % model execution period in second

%% Exported Value
CONTROL_EXPORT_VALUE.period = uint32(CONTROL_CONST.dt*1e3);
CONTROL_EXPORT_VALUE.model_info = int8(['Fixwing Controller ', controller_version, 0]); % 0 for end of string

%% Paramaters
CONTROL_PARAM_VALUE.KP = single(1.0);    
CONTROL_PARAM_VALUE.KI = single(0.1); 
CONTROL_PARAM_VALUE.KD = single(0.1); 

%% Export to Firmware
CONTROL_EXPORT = Simulink.Parameter(CONTROL_EXPORT_VALUE);
CONTROL_EXPORT.CoderInfo.StorageClass = 'ExportedGlobal';
CONTROL_PARAM = Simulink.Parameter(CONTROL_PARAM_VALUE);
CONTROL_PARAM.CoderInfo.StorageClass = 'ExportedGlobal';
```

- Open the simulink model `Controller.slx` and modify it based on your design.

### Using New Model

After creating a new model, the next step is adding the path to the FMT Model project file.

- Click the **Project Path** button under SIMULINK PROJECT page.
- Delete the current used controller model path. e.g. `model/Controller/Base-Controller`.
- Add your new model path. e.g. `model/Controller/Fixwing_Controller`.

That's it, now you should be able to run the simulation with newly added model.
