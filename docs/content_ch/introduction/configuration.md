
## 系统配置

FMT系统可以通过[toml](https://toml.io/en/)来进行配置。这将带来很大的灵活性，你也可以根据需要来对系统进行配置。在启动阶段，系统会尝试从`/sys/sysconfig.toml`加载配置文件。如果这个文件不存在，那么将使用默认的系统配置。默认系统配置仅开启了少量功能（遥控和作动器未开启）。对应目标平台目录中提供了默认的*sysconfig.toml* 文件。你需要将该文件上传到`/sys`目录中以开启所有的系统功能。

比如，[sysconfig.toml](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v5/sysconfig.toml)是pixhawk FMUv5的默认系统配置文件。注意`target`必须跟BSP的目标名称（定义在bsp.h*文件中）相匹配，否则系统将因为断言失败而不会启动，这是为了防止系统使用错误的配置文件。

## Console配置

`[console]`表用来配置控制台模块。如下是一个有效的控制台配置，它将三个设备预留给控制台模块。三个设备`serial0`, `serial1` 和 `mav_console`由`[[console.devices]]`表来进行管理。前两个设备是通用的串口设备，而mav_console是一个特殊的设备（虚拟设备），它提供了read/write接口来跟QGroundControl Mavlink Console进行数据交换。在系统启动阶段，控制台将默认使用第一个设备（在这里是serial0）。当有其它设备收到数据并且`auto-switch`为true，则控制台将自动切换到对应设备上。

```
[console]
    # device list, the first device will be used by default
    [[console.devices]]
    type = "serial"             # type must be serial or mavlink
    name = "serial0"            # device name
    baudrate = 57600            # serial baudrate

    [[console.devices]]
    type = "serial"             # type must be serial or mavlink
    name = "serial1"            # device name
    baudrate = 57600            # serial baudrate
    auto-switch = true          # automatically switch to device if data received

    [[console.devices]]
    type = "mavlink"            # type must be serial or mavlink
    name = "mav_console"        # device name
    auto-switch = true          # automatically switch to device if data received
```

## Mavproxy配置

`[mavproxy]`表用来配置mavproxy模块。它基于mavlink协议实现跟地面站通信的功能。跟控制台配置类似，`[[mavproxy.devices]]`表定义了为mavproxy预留的设备。默认使用第一个设备。

```
[mavproxy]
    [[mavproxy.devices]]        # channel 0
    type = "serial"
    name = "serial1"            # device name
    baudrate = 57600            # serial baudrate

    [[mavproxy.devices]]        # channel 1
    type = "usb"
    name = "usbd0"              # device name
    # automatically switch to usb if connected, switch back to default device is disconnected
    auto-switch = true
```

## Pilot CMD(遥控)配置

`[pilot-cmd]`表用来配置pilot_cmd（遥控）模块。你可以通过`[pilot-cmd.device]`表来配置rc设备，协议，通道数，采样周期，通道数值范围等。遥感通道通过`stick-channel`设置，它包含了四个元素，分别对应左摇杆左/右，左摇杆上/下，右摇杆左/右，右摇杆上/下。一个典型的遥控以及摇杆配置如下所示：

```
    stick-channel = [4,3,1,2]   # channel mapping for [ls_lr,ls_ud,rs_lr,rs_ud]

    [pilot-cmd.device]
    type = "rc"                 # type must be rc
    name = "rc"                 # device name
    protocol = "sbus"           # sbus or ppm
    channel-num = 6             # channel in used, max supported channel: sbus:16, ppm:8
    sample-time = 0.05          # sample time in second (-1 for inherit)
    range = [1000,2000]         # min and max value
```

遥控控制模式可以通过`[[pilot-cmd.mode]]`来设置。比如定义自稳模式：

```
    [[pilot-cmd.mode]]
    mode = 4                    # stabilize mode
    channel = 5
    range = [1800,2000]
```

如果遥控通道5的值在[1800, 2000]的范围内，那么自稳模式将被选中。

FMT支持将遥控多个通道映射到一个控制模式，这将使得有限的遥控通道可以支持更多的模式选择。

```
    [[pilot-cmd.mode]]
    mode = 4                    # stabilize mode
    channel = [5,6]
    range = [[1800,2000],[1400,1600]]
```

你还可以配置遥控指令。当前支持两种类型的指令：

- *cmd1*: 事件指令， 仅短期内有效，类似脉冲信号。
- *cmd2*: 状态指令, 长时间有效，直到有新的状态指令到达，类似步进信号。

可以在`[[pilot-cmd.command]]`表中定义遥控指令，比如：

```
    [[pilot-cmd.command]]
    type = 1                    # 1:event | 2:status
    cmd = 1000                  # force-disarm: forcely disarm motors for safety concern
    channel = 6                 # command channel
    range = [1800,2000]         # if channel value in this range, the event is triggered

    [[pilot-cmd.command]]
    type = 2                            # 1:event | 2:status
    cmd = 2001                          # for testing
    channel = [4, 5]                    # command channel
    range = [[1300,1500],[1200,1400]]   # if channel value in this range, the event is triggered
```

## Actuator配置

`[actuator]`表用来配置作动器设备。`[[actuator.devices]]`表定义系统包含哪些作动器设备。Pixhawk有两个作动器输出端口（main_out和aux_out），所以一个典型的配置如下所示。你可以修改pwm的频率，其中支持的pwm频率为50Hz到400Hz。

```
    [[actuator.devices]]
    protocol = "pwm"            # pwm or dshot
    name = "main_out"           # device name
    freq = 400                  # pwm frequency in Hz

    [[actuator.devices]]
    protocol = "pwm"            # pwm or dshot
    name = "aux_out"            # device name, "main_out" or "aux_out"
    freq = 50                   # pwm frequency in Hz
```

`[[actuator.mappings]]`表可以用来将遥控通道或者控制器输出映射到任意作动器的通道。这里是一个例子。第一个映射将控制器输出通道1,2,3,4映射到main_out作动器的通道1,2,3,4。第二个映射将遥控通道2映射到aux_out作动器的通道2,3,4。

```
    [[actuator.mappings]]
    from = "control_out"
    to = "main_out"
    chan-map = [[1,2,3,4],[1,2,3,4]]

    [[actuator.mappings]]
    from = "rc_channels"
    to = "aux_out"
    chan-map = [[2,2,2],[2,3,4]]
```
