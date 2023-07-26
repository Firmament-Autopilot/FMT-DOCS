
## Debug with J-Link

J-Link is a powerful debug probe which can be used to debug FMT-Firmware code. Please follow these steps to set it up.

1. Install [J-Link Server](https://www.segger.com/downloads/jlink/) on your host system. Normally you could install the latest version.
2. Create a new environment variable `JLINK_SERVER` and set the value to the path of J-Link server. e.g.

Linux/MAC:
```
JLINK_SERVER = <J-link server path>/JLinkGDBServerCLExe
```

Windows:
```
JLINK_SERVER = <J-link server path>/JLinkGDBServerCL
```

3. Set`BUILD = 'debug'` in *rtconfig.py* and rebuild the firmware.

4. Connect Jlink SWD pinout(pin 1,7,9,4) to FMU Debug port. You can also connect J-Link TX/RX for console usage.

<p align="center">
  <img src="./figures/jlink_pinout.png" width="15%">
</p>

5. Install `cortex-debug` expension in VSCode. Ensure that you install version `v1.4.4` or any lower version. This is crucial because higher versions do not support arm-gcc 7-2018-q2-update.

<p align="center">
  <img src="./figures/cortex-debug.png" width="20%">
</p>

5. To initiate the debugging process in VS Code, click on the **Debug Run** button. Then, ensure you select the appropriate configuration for your target. You will find debug configurations for each target already added in the `.vscode/launch` folder. Simply choose the relevant configuration to start debugging your code effortlessly.

<p align="center">
  <img src="./figures/jlink1.png" width="20%">
</p>

7. Click the **Start Debugging** button to begin the debugging process. This action will initiate the download of the debug firmware and halt at the main function, allowing you to inspect the code and run step by step. On the left side, you will find several windows available for you to check, such as watching variable values or examining peripheral data. These windows provide valuable insights and assist you in monitoring relevant information during the debugging process.

<p align="center">
  <img src="./figures/jlink2.png" width="50%">
</p>
