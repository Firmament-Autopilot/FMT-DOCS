
## RC Calibration

The RC calibration is currently not possible done with the QGC because it is designed for PX4, which would calibrate throttle signal to the range of [0 1], as FMT requires the throttle signal to be calibrated to the [-1 1] range.

To solve this problem, FMT provides an internal rc calibration command `rc calib`.

Follow these steps to do the rc calibration.

- Step1：Before calibration, make sure the rc receiver is connected to the flight controller and the rc is turned on. We can use `mcn list` command to check the public frequency of *rc_channels* topics. If it is not 0, that means the rc signal has been received normally.

<img src="figures/rc_calib1.png" width="50%">

- Step2：Type the `rc calib` command and follow the prompts to finish the calibration process.

<img src="figures/rc_calib2.jpg" width="50%">

- Step3：Once complete，Once the calibration is complete, the calibration parameters will be updated to the `RC` parameter group, which can be viewed via the `param list RC` command. The calibrated rc signal is published via `rc_trim_channels` topic, and the max/min/mid values should be 2000/1000/1500 respectively.
- Step4：After confirming that the calibration parameters are correct, enter `param save` in the console to save the calibration results, otherwise the system will lose the unsaved calibration results if the system is powered off.
