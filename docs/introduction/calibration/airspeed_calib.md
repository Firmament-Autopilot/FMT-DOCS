
## Airspeed Calibration

The airspeed calibration is required only for vehicles equipped with an airspeed sensor, such as fixed-wing and VTOL aircraft.

Before calibrating the airspeed sensor, make sure you are in an indoor environment with no wind. 

Next, input the command `calib airspeed` to initiate the calibration process. Once the calibration is completed, the results will be displayed, and it will be stored in the `DIFF_PRESS_OFFSET` parameter. To save the calibration result, use the command `param save`.

```
msh />calib airspeed
diff pressure offset:-93.060173
you need enter param save calibration result
```





