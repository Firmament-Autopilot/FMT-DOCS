
## Code Generation

Using Simulink Embedded Coder can generate C/C++ code from simulink model. All models in `FMT-Model/model` directory are already configured for code generation. You can also modify the code generation configuration by clicking the **Model Configuration Parameters** button.

<img src="figures/model_settings.png" width="50%">

The *ert.tlc* target file is selected to generate code for embedded system. Change it to *grt.tlc* to generate code for desktop environment. You can also change `Language` to either C or C++.

In `Hardware Implementation` page, you can configure the Device Vender and Device Type to make the generated code compatible to the selected hardware.

<img src="figures/hardware_implementation.png" width="50%">

When the model configured is done, you can click the **Build Model** button to generate code. The code should be geneated in the path as listed in `Location of Generated Source Code`.

<img src="figures/codegen.png" width="50%">
