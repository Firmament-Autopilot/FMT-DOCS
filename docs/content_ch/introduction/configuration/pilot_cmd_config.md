## 遥控配置

`[pilot-cmd]`用来对遥控模块进行配置。您可以在`[pilot-cmd.device]`配置遥控的协议，通道数，采样时间，通道数值范围等。摇杆通道可以通过`stick-channel`进行配置，它定义了*yaw*, *throttle*, *roll* and *pitch*对应的遥控通道。一个典型的遥控设备配置如下：

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

**type** 包含如下选项:

- **rc**: 遥控.

| Argument  | Type |  Description |
| ----------- | ------ | ----------- |
| name      | string | devie name       |
| protocol | string | rc protocol (auto, sbus or ppm). auto protocol can automatically decide the protocol (sbus or ppm) that connected but need driver's support      |
| channel-num | interger | rc channels number |
| sample-time | float | rc topic publish sample time |
| range | interger[2] | rc channel value range |

### 模式配置

您可以通过向toml文件中添加`[[pilot-cmd.mode]]`来定义遥控模式，如下是定义自稳模式的例子：

> 目前不支持使用QGC来设置遥控模式。

```
    [[pilot-cmd.mode]]
    mode = 3                    # stabilize mode
    channel = 5
    range = [1800,2000]
```

这个配置的函数时如果遥控通道5的数值在[1800, 2000]的范围内，则自稳模式被选中。这里使用的时校准后的遥控通道值，它将通过`rc_trim_channels`消息发布。您可以通过输入`mcn echo rc_trim_channels`指令来查看各个遥控通道的数值。遥控通道的索引从1开始。

<img src="figures/rc_trim_channels.png" width="30%">

mode的数值定义了遥控模式，它可以被FMS（Flight Management System）识别。完整的遥控模式定义如下所示。您可以添加自己的遥控模式定义，只要能被FMS识别就行。

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

支持使用多个遥控通道来定义模式。对于通道有限的遥控来说，这将可以提供更多的模式选择。

例如如下表示当遥控通道5的数值在[1800, 2000]范围同时遥控通道6自动数值在[1400, 1600]的范围时，定高模式被选中。

```
    [[pilot-cmd.mode]]
    mode = 4                    # altitude mode
    channel = [5,6]
    range = [[1800,2000],[1400,1600]]
```

### 指令配置

指令可以用来通过遥控触发如解锁/上锁，起飞，降落，返航等。指令可以通过`[[pilot-cmd.command]]`来定义，例如

```
    [[pilot-cmd.command]]
    type = 1                    # 1:event | 2:status
    cmd = 1002                  # force-disarm: forcely disarm motors for safety concern
    channel = 6                 # command channel
    range = [1800,2000]         # if channel value in this range, the event is triggered
```

同样的，您可以使用多个遥控通道来定义指令，像我们之前定义遥控模式那样。

完整的指令定义如下所示。您可以定义自己的指令，只要能被FMS识别就行。

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

> 注意指令为变化有效。如果您想要多次触发某条指令，需要先将指令设备为None，然后再将其设置为对应的指令值。