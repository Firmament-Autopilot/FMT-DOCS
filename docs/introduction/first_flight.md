## First Flight

Before the first flight, you need to perform some checks to ensure safety.



#### Check RC

First, you need to check if the remote control function is working properly. First check if the remote control data is received by entering `mcn list` to verify that the publication frequency of the **rc_channels** data is non-zero. If the publication frequency is zero, indicating that no RC signals are received, please ensure that your RC receiver is correctly connected and your sysconfig.toml is properly configured. For more information about RC configuration, please refer to [Pilot-Cmd Configuration](introduction/configuration/pilot_cmd_config.md).

```bash
msh />mcn list
Topic                 #SUB   Freq(Hz)   Echo   Suspend
-------------------- ------ ---------- ------ ---------
sensor_imu0_0           0     1000.0    true    false
sensor_imu0             1     1000.0    true    false
sensor_mag0_0           0      100.0    true    false
sensor_mag0             1      100.0    true    false
sensor_baro             1      100.0    true    false
sensor_optflow          1       0.0     true    false
sensor_rangefinder      1       0.0     true    false
external_state          0       0.0     false   false
ins_output              3      500.0    true    false
external_pos            1       0.0     true    false
fms_output              4      250.0    true    false
control_output          2      500.0    true    false
pilot_cmd               3      19.8     true    false
rc_channels             0      19.8     true    false
rc_trim_channels        1      19.8     true    false
gcs_cmd                 2       1.0     true    false
auto_cmd                1       0.0     true    false
mission_data            2       0.0     true    false
bat_status              0       2.0     true    false

```

Next, check if the joystick mapping is correct. The mapped remote control data is published through the **pilot_cmd** message. Enter `mcn echo pilot_cmd` to view the mapped joystick data. Move the throttle, roll, pitch, and yaw sticks to their extreme positions and the middle position, respectively, and verify if the output data is correct.

When the joystick is pushed all the way down or to the left, the output should be -1. When it is pushed all the way up or to the right, the output should be 1. When the joystick is in the center position, the output should be 0.

```bash
msh />mcn echo pilot_cmd
stick_yaw:0.03 stick_throttle:-0.80 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.70 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.02 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:0.87 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:0.66 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:-0.69 stick_throttle:-0.05 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:-0.81 stick_throttle:-0.02 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:0.03 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.87 stick_throttle:-0.18 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.03 stick_pitch:0.02 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.01 stick_pitch:-0.77 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.01 stick_pitch:0.89 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.01 stick_pitch:0.24 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:-0.83 stick_pitch:-0.01 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.04 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.85 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.03 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
```

> If the error is too large, please recalibrate the remote control.

Next, check if the mode switching is functioning correctly. According to the default toml settings, the SWC switch (RC channel 5) corresponds to the following modes:

- Up: Position mode
- Middle: Altitude mode
- Down: Stabilize mode

By checking the console, verify that the flight controller's console output mode matches the mode settings in sysconfig.toml, which should be either Position, Altitude, or Stabilize, respectively.

```
[1524961] I/StatusTask: [Pilot Mode] Stabilize
[1528078] I/GCS: set Altitude mode.
[1528085] I/StatusTask: [Pilot Mode] Altitude
[1528582] I/GCS: set Position mode.
[1528589] I/StatusTask: [Pilot Mode] Position
```

Finally, check if the emergency lock switch is working correctly. The emergency lock switch is used for one-key locking of the aircraft in emergency situations, such as when the aircraft cannot be automatically locked or when it collides with an obstacle.

Move the SWD (RC channel 6) switch on the remote control from the up position to the down position to trigger the Disarm command, which can be verified through the console.

```
[1519363] I/StatusTask: [FMS Cmd] Disarm
```

> The command is edge-triggered, so moving SWD from the up position to the down position will trigger the Disarm command only once. To trigger it again, you need to move SWD back up and then back down to the down position once more.

<p align="center">
	<img src="./figures/rc_figure.png" width="25%">
</p>


#### Check Arm/Disarm

For the aircraft's arm operation, move the left joystick to the bottom right corner and hold it there, as shown in the figure below. After approximately 1.5 seconds, the aircraft will unlock, and the motors will start idling. Gradually increase the throttle, when the throttle surpasses a threshold (usually the center position), it will transition from standby to the arm state.

In the standby  mode, move the left joystick to the bottom-left corner and hold it there; approximately 1.5 seconds later, the aircraft will be locked.

<p align="center">
	<img src="./figures/arm_disarm.png" width="25%">
</p>


After unlocking the aircraft, reduce the throttle. When the aircraft touches the ground, pull the throttle to the minimum position, and the aircraft will automatically lock.

The aircraft's arm and disarm operations can also be performed by QGroundControl (QGC) ground station.

<p align="center">
	<img src="./figures/qgc_arm.png" width="40%">
</p>

#### Check Motors

First, check the installation order and rotation direction of the motors. This is crucial because installing the motors incorrectly can lead to the destruction of the aircraft. To verify the motor installation order and direction, please refer to the [Vehicle/Airframe](introduction/vehicle_type.md) section.

To verify the motor installation, you can use `act` command to drive each motor individually. **However, before doing so, ensure all motor propellers are removed for safety reasons**. 

First disable the output of the controller to prevent it from overwriting the output of the act instruction.

```
mcn suspend control_output
```

To select the motor channel, you can use the `-c` or `--channel` option, followed by a hexadecimal value representing the channel bit mask. For example, using the following command can drive or stop motor 1. Replace "1" with "2" for motor 2, "4" for motor 3, and "8" for motor 4 to drive or stop each corresponding motor.

```
act set -d main_out -c 1 1200
act set -d main_out -c 1 1000
```

After testing, you can resume the output of the controller.

```
mcn resume control_output
```

To simplify the process, you can add all these commands into a script named `check_motor.sh` as shown below.

```
mcn suspend control_output
act set -d main_out -c 1 1200
delay 3000
act set -d main_out -c 1 1000
delay 1000
act set -d main_out -c 2 1200
delay 3000
act set -d main_out -c 2 1000
delay 1000
act set -d main_out -c 4 1200
delay 3000
act set -d main_out -c 4 1000
delay 1000
act set -d main_out -c 8 1200
delay 3000
act set -d main_out -c 8 1000
delay 1000
mcn resume control_output
```

And then run the script by executing the following command.

```
exec check_motor.sh
```

#### Check Attitude

Before the flight, you should also verify if the attitude is correct via QGroundControl (QGC). This is to ensure that your flight controller is mounted at the right position and direction, and that your sensors are correctly calibrated.

#### Simulated Flight

For users who are not familiar with aircraft controls, it is recommended to first practice flying using the SIH simulation mode to become familiar with the aircraft's operation. The operation of SIH is completely consistent with that of real aircraft. For instructions on how to enable the SIH simulation mode, please refer to the simulation steps in [Simulation-In-Hardware](simulation/SIL.md).

#### Real Flight

For safety reasons, it is recommended to conduct tests in a spacious outdoor area. For beginners, it is advisable to start with the Position mode for flying.

