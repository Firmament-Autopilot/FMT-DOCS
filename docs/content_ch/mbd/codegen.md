
## 代码生成

使用 Simulink Embedded Coder 可以将 simulink 模型生成 C/C++ 代码。`FMT-Model/model` 目录下的所有模型都已经配置过，可以直接生成代码。你也可以点击 **Model Configuration Parameters** 按钮来修改代码生成的配置。

<img src="figures/model_settings.png" width="50%">

选择 *ert.tlc* 目标文件用来生成嵌入式代码。如果要生成桌面端代码，请选择 *grt.tlc* 。你也可以修改 `Language` 选项来生成 C 或者 C++ 代码。

在 `Hardware Implementation` 页面，你可以配置 Device Vender 和 Device Type 来适配不同的硬件平台。

<img src="figures/hardware_implementation.png" width="50%">

当模型配置完成后，你可以点击 **Build Model** 按钮来生成代码。`Location of Generated Source Code` 列出了生成代码的路径。

<img src="figures/codegen.png" width="50%">
