
## Code Generation

The models located within the `FMT-Model/model` directory, specifically the INS, FMS, Controller, and Plant models, have already been appropriately configured to support code generation. By utilizing Simulink Embedded Coder, it is possible to generate C/C++ code that corresponds to the target specified. This facilitates the translation of these models into executable code, allowing for seamless integration into the embedded system FMT-Firmware.

In order to generate the code for a specific model, you need to first open the model. Once the model is open, locate and select the "Build Model" button. This action will initiate the code generation process. The resulting code will be generated and stored in the directory specified by the "Location of Generated Source Code". Browse to this location to access the generated code files.

<img src="figures/codegen.png" width="50%">



### Code Generation Configuration

Furthermore, you have the option to customize the code generation configuration based on your specific requirements. This can be done by clicking on the "Model Configuration Parameters" button. By accessing the model configuration parameters, you can modify various settings and options related to code generation. This flexibility allows you to tailor the code generation process to meet your specific needs and preferences.

<img src="figures/model_settings.png" width="50%">

To generate code for a desktop environment instead of an embedded system, it is necessary to switch the selected target file from *ert.tlc* to *grt.tlc*. This can be done within the code generation configuration settings.

Additionally, you have the option to change the `Language` parameter to either C or C++, depending on your programming language preference.

In the `Hardware Implementation` page, you can configure the `Device Vendor` and `Device Type` settings to ensure compatibility of the generated code with the selected hardware.

It is worth mentioning that browsing through the other pages within the configuration settings provides access to a wider range of options for further customization. These additional pages offer various choices and configurations that can be explored to enhance and fine-tune the code generation process according to your specific needs.

<img src="figures/hardware_implementation.png" width="50%">
