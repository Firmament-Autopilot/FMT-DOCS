
## 代码生成

当算法模型在Simulink环境中功能测试完成后，可以直接一键生成目标硬件平台的代码。位于 `FMT-Model/model` 目录中的模型，包括 INS、FMS、Controller 和 Plant 模型，已经默认配置为基于Simulink Embedded Coder生成Cortex目标平台的C代码。生成的代码经过高度优化和检查，从而确保其效率和正确性，生成的代码可以与 FMT-Firmware 嵌入式软件系统无缝集成。


#### 模型生成代码

首先打开待生成代码的模型，可以是Plant/INS/FMS/Controller中的任意一种，注意，其它仿真模型，如MILSIM.slx不支持代码生成。

模型打开后，找到并选择“构建模型”按钮。此操作将启动代码生成过程。

<p align="center">
 <img src="content_cn/mbd/figures/model_build_btn.png" width="40%">
</p>

等待模型编译，代码生成过程完成后，将弹出模型代码生成报告，如下所示。其中`Localtion of Generated Source COde`就是生成代码的存放路径。

<p align="center">
 <img src="content_cn/mbd/figures/codegen.png" width="40%">
</p>


#### 代码生成配置

点击 `Model Configuration Parameters `按钮，即可进入模型配置页面。在这里，您可以修改与代码生成相关的各种设置和选项。

<p align="center">
 <img src="content_cn/mbd/figures/model_settings.png" width="40%">
</p>

比如要生成PC桌面端代码而不是嵌入式系统，可以将所选目标文件从 `ert.tlc` 切换为 `grt.tlc`。

此外，您可以根据您的编程语言偏好，将语言参数更改为 C 或 C++。

值得一提的是，浏览配置设置中的其他页面可以访问更多选项进行进一步自定义，比如修改生成代码的目标硬件平台。这些额外的页面提供了各种选择和配置，您可以探索它们以根据您的具体需求增强和微调代码生成过程。

<p align="center">
	<img src="content_cn/mbd/figures/hardware_implementation.png" width="40%">
</p>
