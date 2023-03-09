
## 系统配置

可以方便的配置系统组件而无需重新编译整个系统是很重要的。FMT是一个高度可定制化的系统，并提供了一个[TOML](https://toml.io/en/)文件来配置系统组件。这将给用户根据需求来配置系统提供极大的灵活性。

当FMT系统运行起来的时候，它会去自动寻找板载文件系统上的`/sys/sysconfig.toml`配置文件。如果这个文件存在，则会加载里面的配置项。如果这个文件不存在，则会使用`default_config.h`中固化的默认系统配置。注意，默认配置只开启了有限的功能，遥控和电机输出功能都未开启。

每个BSP下的config目录都包含一个默认的配置文件*sysconfig.toml*。为了开启FMT的所有功能，用户需要将*sysconfig.toml*配置文件上传到板子的*/sys*目录。这个文件包哦含了模块功能需要的配置信息。

- [Amov ICF5 Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/amov/icf5/config/sysconfig.toml)
- [CUAV V5+ Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/amov/icf5/config/sysconfig.toml)
- [HEX Cubeorange Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/cubepilot/cubeorange/config/sysconfig.toml)
- [Pixhawk 4 Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v5/config/sysconfig.toml)
- [Pixhawk 2.4.6 Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v2/config/sysconfig.toml)

比如如下就是pixhawk4的默认`sysconfig.toml`配置文件。注意target的值需要跟BSP的目标名称相匹配（定义在*bsp.h*中）。不然的话系统将无法启动，为了防止使用错误的配置信息。

```
# FMT System Configuration File

# The target should match with BSP's target name
target = "Pixhawk4 FMUv5"

# Console Configuration
[console]
    [[console.devices]]
    type = "serial"
    name = "serial0"
    baudrate = 57600
    auto-switch = true

    [[console.devices]]
    type = "mavlink"
    name = "mav_console"
    auto-switch = true

# Mavproxy Device Configuration
[mavproxy]
    [[mavproxy.devices]]
    type = "serial"
    name = "serial1"
    baudrate = 57600

    [[mavproxy.devices]]
    type = "usb"
    name = "usbd0"
    auto-switch = true

# Pilot CMD Configuration
[pilot-cmd]
    # channel mapping for [yaw, throttle, roll, pitch]
    stick-channel = [4,3,1,2]

    [pilot-cmd.device]
    type = "rc"
    name = "rc"
    protocol = "sbus"           # sbus or ppm
    channel-num = 6             # max supported channel: sbus:16, ppm:8
    sample-time = 0.05          # sample time in second (-1 for inherit)
    range = [1000,2000]

    [[pilot-cmd.mode]]
    mode = 5                    # Position mode
    channel = 5
    range = [1000,1200]

    [[pilot-cmd.mode]]
    mode = 4                    # Altitude mode
    channel = 5
    range = [1400,1600]

    [[pilot-cmd.mode]]
    mode = 3                    # Stabilize mode
    channel = 5
    range = [1800,2000]

    [[pilot-cmd.command]]
    type = 1                    # 1:event | 2:status
    cmd = 1002                  # FMS_Cmd_Disarm
    channel = 6
    range = [1800,2000]

# Actuator Configuration
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
    from = "control_out"
    to = "main_out"
    chan-map = [[1,2,3,4],[1,2,3,4]]
```

更多的配置信息内容，请参考接下来的章节。