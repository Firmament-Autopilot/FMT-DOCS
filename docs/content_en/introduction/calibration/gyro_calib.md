
## Perform Calibration

The gyroscopic calibration process is done via the QGroundControl interface. Follow the guide of QGroundControl to complete the calibration process.

1. Place the vehicle on a flat surface and keep it still, then click the Gyroscope sensor button.

<img src="figures/gyro_calib1.png" width="50%">

2. Click OK to start calibration. The bar at the top shows the progress.

<img src="figures/gyro_calib2.png" width="50%">

3. When finished, QGroundControl will display a progress bar *Calibration complete*.

<img src="figures/gyro_calib3.png" width="50%">

## Check & Save Result

1. The calibration result can be seen by typing `param list CALIB` in console.

```
msh />param list CALIB
CALIB:
      GYRO0_XOFF: -0.024290
      GYRO0_YOFF: -0.059920
      GYRO0_ZOFF: -0.007062
......
```

2. Save the calibration result by typing `param save` in console, otherwise unsaved calibration result will lost when system power-off.
