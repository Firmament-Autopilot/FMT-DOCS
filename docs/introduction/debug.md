
## Debug with J-Link

J-Link is a powerful debug probe which can be used to debug FMT-Firmware code. Please follow these steps to set it up.

1. Install [J-Link Server](https://www.segger.com/downloads/jlink/) on your host system. Normally you could install the latest version.
2. Create a new environment variable `JLINK_SERVER` and set the value to the path of J-Link server. e.g.

Linux:
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

5. Install `cortex-debug` expension in VSCode. You need install `v1.4.4` or lower version.

<p align="center">
  <img src="./figures/cortex-debug.png" width="20%">
</p>

5. Click **Debug Run** button in VS Code and **select the right configuration** for your target. We've already added debug configurations in the *.vscode/launch* for each target.

<p align="center">
  <img src="./figures/jlink1.png" width="20%">
</p>

7. Click **Start Debugging** button.

<p align="center">
  <img src="./figures/jlink2.png" width="50%">
</p>
