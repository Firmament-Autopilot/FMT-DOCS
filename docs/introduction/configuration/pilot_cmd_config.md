## Pilot CMD (RC) Configuration

`[pilot-cmd]` table is used to configure the pilot_cmd (rc) module. You can configure the rc device, protocol, channel number, sample time, channel ranges, etc in `[pilot-cmd.device]` subtable. The stick channel is configured by `stick-channel`, which is an array with 4 elements represents to the channel value for *yaw*, *throttle*, *roll* and *pitch*. A typical pilot-cmd device configuration with stick-channel is shown below:

```
    # channel mapping for [yaw, throttle, roll, pitch]
    stick-channel = [4,3,1,2]

    [pilot-cmd.device]
    type = "rc"
    name = "rc"
    protocol = "auto"           # auto/sbus/ppm
    channel-num = 8             # max supported channel: sbus:16, ppm:8
    sample-time = 0.05          # sample time in second (-1 for inherit)
    range = [1000,2000]
```

The **type** has following choices:

- **rc**: remote controller.

| Argument  | Type |  Description |
| ----------- | ------ | ----------- |
| name      | string | devie name       |
| protocol | string | rc protocol (auto, sbus or ppm). auto protocol can automatically decide the protocol (sbus or ppm) that connected but need driver's support      |
| channel-num | interger | rc channels number |
| sample-time | float | rc topic publish sample time |
| range | interger[2] | rc channel value range |

### Mode Configuration

To define pilot control mode, you can add `[[pilot-cmd.mode]]` to toml file. Below is an example to define the stabilize mode:

> Do not use QGC to configure pilot mode, which is not working. 

```
    [[pilot-cmd.mode]]
    mode = 3                    # stabilize mode
    channel = 5
    range = [1800,2000]
```

The meaning of this configuration is that if rc channel 5 value is in the range of [1800, 2000], the stabilize mode will be selected. The calibrated rc value is used here, which is published via `rc_trim_channels` topic. You can see the values of rc channel using `mcn echo rc_trim_channels`. The rc channels index start from 1.

<img src="figures/rc_trim_channels.png" width="30%">

The value of mode defines the pilot mode, which is recognizable by FMS (Flight Management System). The full definition of pilot mode is shown below. You can also define your own pilot mode as long as FMS can recognize it.

| Mode | Value | Description |
|----------|----------|----------|
|   None |    0   | mode that vehicle won't do anything. |
|   Manual |    1   | mode where stick input is sent directly to actuators for fully manual control. |
|   Acro |    2   | mode for performing acrobatic maneuvers. RPY stick inputs are translated to angular rate commands that are stabilized by autopilot. Throttle is passed directly to control allocation. |
|   Stabilize |    3   | mode where centered RP sticks levels vehicle attitude (roll and pitch) and Y stick translated to yaw angular rate. The vehicle altitude is not maintained and you need Manipulate the throttle stick to maintain altitude.  |
|   Altitude |    4   | mode similar to Stabilize, except that altitude is maintained. throttle stick translated to z-axis velocity. |
|   Position |    5   | mode where position and altitude are hold with centered sticks. move sticks translated to velocity of the corresponding axis. |
|   Mission |    6   | mode that execute the mission data defined in */sys/mission.txt* |
|   Offboard |    7   | mode that accept external command via *auto_cmd* topic. |

It is also possible to map multiple rc channels to a single mode, which provides more mode choices with limited rc channels. 

For instance, the following setting means If rc channels 5 in range [1800, 2000] and channel 6 in range [1400, 1600], the altitude mode is selected.

```
    [[pilot-cmd.mode]]
    mode = 4                    # altitude mode
    channel = [5,6]
    range = [[1800,2000],[1400,1600]]
```

### Command Configuration

The fms command is used to send command via rc, such as arm/disarm, takeoff, landing, return, etc. The fms command can be defined with `[[pilot-cmd.command]]` subtables, e.g,

```
    [[pilot-cmd.command]]
    type = 1                    # 1:event | 2:status
    cmd = 1002                  # force-disarm: forcely disarm motors for safety concern
    channel = 6                 # command channel
    range = [1800,2000]         # if channel value in this range, the event is triggered
```

You can also map multiple rc channels to a single command like we did for pilot mode.

The full definition of fms command is shown below. You can also define your own fms command as long as FMS can recognize it.

| Command | Value | Description |
|----------|----------|----------|
|   None |    0   | idle command that vehicle won't do anything. |
|   PreArm |    1000   | pre-arm the vehicle to enter standby state. |
|   Arm |    1001   | arm the vehicle to enter arm state (skip standby state). |
|   Disarm |    1002   | disarm the vehicle. |
|   Takeoff |    1003   | vehicle takeoff to a pre-defined altitude. |
|   Land |    1004   | land the vehicle at current position. |
|   Return |    1005   | return to home position then land. |
|   Pause |    1006   | pause the current mission. |
|   Continue |    1007   | continue the mission. |

> Note that the command is valid if value changed. So if you want to trigger a command twice, you need first set command to *None* and then set to a valid command value.