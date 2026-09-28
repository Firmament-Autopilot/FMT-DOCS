
## Download Firmware

The FMT code is available on [Github](https://github.com/Firmament-Autopilot). To download the FMT-Firmware, use the following git command. If you don't have [git](https://git-scm.com/downloads) installed, please install it before proceeding.

```
git clone https://github.com/Firmament-Autopilot/FMT-Firmware.git --recursive --shallow-submodules
```

> Please be aware that the FMT-Firmware includes submodules. To ensure you download the submodules as well, use the *--recursive* flag when fetching the repository. 

## Toolchain

FMT utilizes cross-platform toolchains, enabling its development across various operating systems, such as Windows, Linux, and Mac. This flexibility ensures that developers can work on the project seamlessly, regardless of their preferred platform.

### Compiler

FMT-Firmware specifically relies on the **arm-none-eabi- toolchain 7-2018-q2-update** version, which can be obtained from [this link](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads). It is essential to adhere to this specified compiler version to prevent any unforeseen errors or unpredictable behaviors during development. Using other compiler versions is not recommended to maintain stability and consistency in the project.

After installing the compiler, the next step is to create a new environment variable called `RTT_EXEC_PATH`. This variable should point to the full path of the compiler's bin directory. Here's how you can set up the environment variable:

1. Find the path to the compiler's bin directory, which contains executable files like `gcc`, `gdb`, etc.

2. Set the `RTT_EXEC_PATH` environment variable to this full path. The process varies depending on your operating system:

   - **Windows**: Open the Control Panel, go to System and Security > System > Advanced system settings > Environment Variables. Click on "New" under "User variables" and enter the variable name as `RTT_EXEC_PATH` and the variable value as the full path to the compiler's bin directory.

    <p align="center">
    	<img src="./figures/win_exec_path.png" width="30%">
    </p>

   - **Linux/Mac**: Open a terminal and edit the shell configuration file (e.g., `~/.bashrc`, `~/.bash_profile`, or `~/.zshrc`, depending on your shell). Add the following line to the file:

   ```bash
   export RTT_EXEC_PATH=/path/to/compiler/bin
   ```

   Save the file and run `source ~/.bashrc` (or the corresponding file for your shell) to apply the changes to the current terminal session.

By setting the `RTT_EXEC_PATH` environment variable, you enable the system to locate and use the compiler tools easily during the development process.

### Construction Tool

FMT employs [SCons](https://scons.org/) as its construction tool, which serves as an enhanced and cross-platform alternative to the traditional Make utility. SCons configuration files are written in Python, offering a user-friendly and powerful approach to address build-related challenges. Python's flexibility and ease of use make configuring the build process a more intuitive and efficient experience. With SCons, developers can enjoy a streamlined and effective build system that adapts smoothly across different platforms.

Prior to installing SCons, it is essential to verify whether Python 3 is already installed on your system. If you don't have Python 3 installed, you can download it from [here](https://www.python.org/downloads/). Python serves as a prerequisite for SCons, as SCons configuration files are written in Python scripts. Once Python 3 is installed, you can proceed with the installation of SCons to enable a smoother and more efficient build process for your projects.

After ensuring that Python 3 is installed on your system, follow these steps in the terminal:

1. To install the latest version of SCons, enter the following command:

   ```bash
   > pip3 install scons
   ```

   > If this command is not working on your system, you can download [scons local package](https://scons.org/pages/download.html). The scons-local packages contain versions of SCons that you can execute standalone, out of a local directory, without installation. **Don't forget to add SCons path to your enviroment parameter**.

2. Once the installation is complete, you can check if SCons was installed successfully by entering the following command:

   ```bash
   > scons --version
   ```

3. If the version information is printed on the terminal, it indicates that SCons has been installed successfully, and you are now ready to use it as your construction tool for projects.

    ```bash
    > scons --version
    SCons by Steven Knight et al.:
            SCons: v4.4.0.fc8d0ec215ee6cba8bc158ad40c099be0b598297, Sat, 30 Jul 2022 14:11:34 -0700, by bdbaddog on M1Dog2021
            SCons path: ['C:\\Users\\zouji\\AppData\\Local\\Programs\\Python\\Python311\\Lib\\site-packages\\SCons']
    Copyright (c) 2001 - 2022 The SCons Foundation
    ```

### IDE

Using Visual Studio Code (VS Code) as the Integrated Development Environment (IDE) is highly recommended for working with FMT and SCons. You can download VS Code from [the official website](https://code.visualstudio.com/).

VS Code provides a powerful and user-friendly development environment with a wide range of extensions and features, making it ideal for working on projects like FMT that utilize SCons as the build tool. Its flexibility, extensibility, and compatibility with various programming languages make it a popular choice among developers.

To begin coding for the FMT-Firmware project in Visual Studio Code, follow these steps:

1. Open Visual Studio Code.
2. Click on the "File" menu in the top-left corner.
3. Select "Open Folder" from the dropdown menu.
4. A file explorer dialog will appear. Navigate to the location where you have the FMT-Firmware folder stored.
5. Select the FMT-Firmware folder and click "Open."

Now, you have the FMT-Firmware project opened in Visual Studio Code, and you can start coding, modifying, and managing the project using the various features and extensions provided by the IDE. Happy coding!

<p align="center">
  <img src="./figures/vscode.png" width="30%">
</p>

Absolutely, installing useful Visual Studio Code (VS Code) plugins can significantly enhance your development experience when working with the FMT-Firmware project. Two essential plugins for the FMT-Firmware project are:

1. **C/C++**: This plugin provides excellent support for C and C++ languages in VS Code, offering features like code highlighting, IntelliSense, code navigation, and debugging capabilities tailored for these languages.
2. **Clang-Format**: Clang-Format is a code formatting tool for C, C++, and other programming languages. The Clang-Format plugin in VS Code allows you to automatically format your code according to specific style guidelines, ensuring consistent and readable code.

To install these plugins, follow these steps:

1. Open Visual Studio Code.
2. Click on the "Extensions" icon on the left sidebar (or use the shortcut `Ctrl+Shift+X` or `Cmd+Shift+X` on macOS).
3. In the Extensions marketplace search bar, type "C/C++" and "Clang-Format."
4. Click on the "Install" button for each plugin.
5. After installation, you may need to restart Visual Studio Code to activate the plugins.

With these plugins installed, your development environment will be better equipped to handle the intricacies of the FMT-Firmware project, making coding and formatting tasks more efficient and convenient.

