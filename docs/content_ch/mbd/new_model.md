
## 添加新模型

基于 FMT Model 的框架可以快速添加新的模型，或者基于已有的模型进行二次开发。我们这里使用一个例子来说明如何添加一个新的控制器模型。

### 创建新模型

请按照如下步骤来添加一个控制器模型到 FMT Model:

- 复制一个已有的控制器模型文件夹并重命名，比如 `Fixwing_Controller/`。
- 该文件夹下包含如下文件
  - **Controller.slx**: 控制器 simulink 模型文件.
  - **controller_model_init.m**: 模型初始化脚本，由 FMT Model 调用.
  - **LICENSE**: 模型的许可.
  - **README.md**: 模型介绍.

<img src="figures/template_folder.png" width="50%">

- 根据你的模型修改 `controller_model_init.m` 文件的内容。

controller_model_init.m 的内容示例如下所示。模型的执行周期为 500Hz，可以通过修改 `CONTROL_CONST.dt` 来设定。你也可以为你的模型添加参数，这些参数可以在嵌入式层访问。

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

- 打开 simulink 模型 `Controller.slx` 并根据你的设计进行修改。

### 使用新模型

在创建了新的模型后，下一步就是将其添加到 FMT Model 的工程路径文件。

- 点击 SIMULINK PROJECT 界面的 **Project Path** 按钮
- 删除当前控制器模型的路径，比如 `model/Controller/Base-Controller`
- 添加新模型的路径，比如 `model/Controller/Fixwing_Controller`

好了，现在你应该可以使用新添加的模型进行仿真了。
