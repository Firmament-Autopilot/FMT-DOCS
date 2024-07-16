
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
<p align="center">
  <img src="figures/template_folder.png" width="80%">
</p>

- 通过将您的算法集成到现有结构中，修改 Controller.slx 文件，以便无缝集成您的具体实现。
- 请更新 controller_model_init.m 文件的内容，使其与您的模型规格保持一致。这包括修改模型参数、定义适当的模型周期、模型信息（名称、版本等），并根据您的具体模型需求调整其他相关配置。

以下是 `controller_model_init.m` 文件的摘录，作为示例。可以修改模型信息，例如 `model_version` 和 `model_name`，这些信息将显示在系统横幅中。模型的执行周期可以通过调整 `CONTROL_CONST.dt` 的值来定义，单位为秒。此外，还可以包含特定于模型的参数，这些参数将导出到 FMT-Firmware，并在嵌入式层中可用。

```matlab
% 
% This is a template controller model
% Firmament Autopilot
%
model_version = 'v0.0.1';    % model version
model_name = 'Base Controller';    % model name

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

### 使用新模型

在创建了新的模型后，下一步就是将其添加到 FMT Model 的工程路径文件。

1. 导航至 SIMULINK PROJECT 页面并点击“项目路径”按钮。

2. 移除当前使用的控制器模型的现有路径，例如 `model/Controller/Base-Controller`。

3. 添加您新创建的模型路径，例如 `model/Controller/My_Controller`。

4. 关闭 Simulink 项目 - FMT_Model，并重新执行 `FMT_Model.prj`，这样将成功地将您的新模型添加到项目路径中。

好了，现在你应该可以使用新添加的模型进行仿真了。
