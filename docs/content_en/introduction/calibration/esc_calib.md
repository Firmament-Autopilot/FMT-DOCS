
## ESC Calibration

The calibration updates all the ESCs with the maximum and minimum PWM input values so that all the ESCs will respond to PWM input in the same way.

## Calibration Method

Different ESCs may have different calibration methods. For details, please refer to the instructions of the corresponding ESCs. Here, only the general ESC calibration methods are described.

- Power on the flight controller
- Set the corresponding motor output to the maximum value (such as 2000)
- Connect the battery and power on the ESCs/motors
- After hearing the beep sound, set the corresponding motor output to the minimum value (such as 1000)
- Calibration complete

## Calibration via RC

The simplest calibration method is to directly map the rc channel to the corresponding motor output. For example, to map the throttle stick channel to the four main motor outputs and cancel the mapping from the controller to the motor output at the same time, you can configure as follows.

```
[actuator]
    [[actuator.devices]]
    protocol = "pwm"
    name = "main_out"
    freq = 400                  # pwm frequency in Hz

    [[actuator.devices]]
    protocol = "pwm"
    name = "aux_out"
    freq = 400                  # pwm frequency in Hz

    [[actuator.mappings]]
    from = "rc_channels"
    to = "main_out"
    chan-map = [[3,3,3,3],[1,2,3,4]]
```

In this way, the output of the main motor will be directly determined by channel 3 (throttle stick) of the rc, and then calibrated according to the ESC calibration method.

## Calibration via Command

Because the rc channel itself may have certain errors, this will cause errors in the ESC after calibration. In order to avoid the error of the rc channel itself affecting the ESC calibration result, we can use the `act` command to calibrate.

Similarly, we need to disable the output of the controller first to prevent it from overwriting the output of the act instruction. To disable the controller's output, we can simply run the following command:

```
mcn suspend control_output
```

Then we can enter the following command in the console to set the output of the corresponding motor to the maximum or minimum value:

```
act set --all -d main_out 2000
act set --all -d main_out 1000
```