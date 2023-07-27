
## Add New Model

The FMT-Model offers a comprehensive framework that facilitates the streamlined development of new models or the re-development of existing ones. To illustrate the process of integrating a new controller model, we provide a practical example.

### Create New Model

To add a new controller model into the FMT-Model, kindly adhere to the following guidelines:

- Duplicate an existing controller model folder or generate a new one from scratch, ensuring it is suitably named, such as `New_Controller/`. This folder should include the following essential files:
    - *Controller.slx*: This file comprises the Simulink model for the controller.
    - *controller_model_init.m*: The model initialization script that is executed by the FMT Model.
    - *LICENSE*: This file contains the license details for your model.
    - *README.md*: A comprehensive introduction and documentation of the model.

<p align="center">
	<img src="./figures/template_folder.png" width="40%">
</p>

- Modify the `Controller.slx` file by incorporating your algorithm into the existing structure, allowing for the seamless integration of your specific implementation.
- Please update the contents of the `controller_model_init.m` file to align with the specifications of your model. This includes modifying model parameters, defining the appropriate model period, model information (name, version, etc.), and adjusting any other relevant configurations in accordance with your specific model requirements..

The following is an excerpt from the `controller_model_init.m` file, serving as an example. It is possible to modify the model information, such as `model_version` and `model_name`, which will be displayed in the system banner. The execution period of the model can be defined by adjusting the value of `CONTROL_CONST.dt`, measured in seconds. Additionally, it is feasible to include parameters specific to your model, which will be exported to the FMT-Firmware and made accessible within the embedded layer.

```matlab
% 
% This is a template controller model
% Firmament Autopilot
%
model_version = 'v0.0.1';	% model version
model_name = 'Base Controller';	% model name

%% load configuration
load('control_default_config.mat');

%% Constant Variable
CONTROL_CONST.dt = 0.002;   % model execution period

%% Exported Value
CONTROL_EXPORT_VALUE.period = uint32(CONTROL_CONST.dt*1e3);
CONTROL_EXPORT_VALUE.model_info = int8([model_name, ' ', model_version, 0]); % 0 for end of string

%% Paramaters
CONTROL_PARAM_VALUE.KP = single(1.0);    
CONTROL_PARAM_VALUE.KI = single(0.1); 
CONTROL_PARAM_VALUE.KD = single(0.1); 

%% Export to firmware
CONTROL_EXPORT = Simulink.Parameter(CONTROL_EXPORT_VALUE);
CONTROL_EXPORT.CoderInfo.StorageClass = 'ExportedGlobal';
CONTROL_PARAM = Simulink.Parameter(CONTROL_PARAM_VALUE);
CONTROL_PARAM.CoderInfo.StorageClass = 'ExportedGlobal';
```

### Using New Model

Upon the creation of a new model, the subsequent procedure involves incorporating the model path to the FMT-Model project file.

1. Navigate to the SIMULINK PROJECT page and click the "Project Path" button.
2. Remove the existing path for the currently utilized controller model, such as `model/Controller/Base-Controller`.
3. Integrate the path for your newly created model, for instance, `model/Controller/My_Controller`.
4. Close Simulink Project - FMT_Model and re-execute the FMT_Model.prj, which will result in the successful addition of your new model to the project path.

Following the completion of the aforementioned steps, you should now be able to execute the simulation using the newly added model.
