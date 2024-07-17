
## 代码生成

位于 `FMT-Model/model` 目录中的模型，特别是 INS、FMS、Controller 和 Plant 模型，已经适当配置以支持代码生成。通过使用 Simulink Embedded Coder，可以生成与指定目标对应的 C/C++ 代码。这促进了这些模型向可执行代码的转换，从而实现与嵌入式系统 FMT-Firmware 的无缝集成。

要为特定模型生成代码，首先需要打开该模型。一旦模型打开，找到并选择“构建模型”按钮。此操作将启动代码生成过程。生成的代码将存储在“生成源代码位置”指定的目录中。浏览到此位置即可访问生成的代码文件。
<p align="center">
 <img src="figures/codegen.png" width="45%">
</p>


## 代码生成配置

您可以根据具体需求自定义代码生成配置。点击 `Model Configuration Parameters `按钮，即可进入模型配置参数页面。在这里，您可以修改与代码生成相关的各种设置和选项。这种灵活性使您能够调整代码生成过程，以满足您的特定需求和偏好。

<p align="center">
 <img src="figures/model_settings.png" width="45%">
</p>

要为桌面环境生成代码而不是嵌入式系统，需要将所选目标文件从 `ert.tlc` 切换为 `grt.tlc`。这可以在代码生成配置设置中完成。

此外，您可以根据您的编程语言偏好，将语言参数更改为 C 或 C++。

在硬件实现页面中，您可以配置设备供应商和设备类型设置，以确保生成的代码与所选硬件兼容。

值得一提的是，浏览配置设置中的其他页面可以访问更多选项进行进一步自定义。这些额外的页面提供了各种选择和配置，您可以探索它们以根据您的具体需求增强和微调代码生成过程。

<p align="center">
	<img src="./figures/hardware_implementation.png" width="45%">
</p>
