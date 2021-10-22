
## Debug with J-Link

1. Install [J-Link Server](https://www.segger.com/downloads/jlink/) on your host system.
2. Create a new enviroment variable `JLINK_SERVER` and set the value to the path of J-Link server. e.g.

Linux:
```
JLINK_SERVER = $JLink/JLinkGDBServerCLExe
```

Windows:
```
JLINK_SERVER = $JLink/JLinkGDBServerCL
```

3. Set`BUILD = 'debug'` in *rtconfig.py* and rebuild the firmware.

4. Connect Jlink SWD pinout(pin 1,7,9,4) to FMU Debug port. You can also connect J-Link TX/RX for console usage.

<img src="figures/jlink_pinout.png" width="15%">

5. Install `cortex-debug` expension in VSCode.

6. Click **Debug Run** button in VS Code and select the right configuration for your target.

<img src="figures/jlink1.png" width="20%">

7. Click **Start Debugging** button.

<img src="figures/jlink2.png" width="50%">

## Pixhawk FMU Pinout

For more information about pixhawk pintout, please check the following link.

[Pixhawk4 pinout](http://www.holybro.com/manual/Pixhawk4-Pinouts.pdf)

[Pixhawk2 pinout](https://docs.px4.io/master/en/flight_controller/pixhawk.html)
