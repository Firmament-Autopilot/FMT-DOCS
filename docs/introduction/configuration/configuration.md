
## System Configuration

It's necessary to configure the system modules without recompiling the whole system. FMT is a highly customizable system and provides a way to configure modules via a [TOML](https://toml.io/en/) file, which gives users great flexibility to configure the system to their desired settings. 

When the FMT system is started up, it will automatically look for the system configuration file `/sys/sysconfig.toml` on the onboard file system. If this file exists, the system will load the configuration settings within it. However, if the file is not present, the system use the default configuration which is hardcoded in `default_config.h`. Note that the default configuration has a limited set of functions, with both the rc and actuator modules disabled.

Each BSP contains a default *sysconfig.toml* under the config directory. To ensure that the FMT system is fully functional, users must upload a *sysconfig.toml* file to the */sys* folder onboard. This file contains the necessary settings for modules to be functional.

- [Amov ICF5 Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/amov/icf5/config/sysconfig.toml)
- [CUAV V5+ Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/amov/icf5/config/sysconfig.toml)
- [HEX Cubeorange Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/cubepilot/cubeorange/config/sysconfig.toml)
- [Pixhawk 4 Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v5/config/sysconfig.toml)
- [Pixhawk 2.4.6 Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v2/config/sysconfig.toml)

For instance, the following is the default *sysconfig.toml* for pixhawk4. Be noticed the target value should match with BSP's target name defined in *bsp.h*. Otherwise the system won't boot because of unmatched target to prevent using of a wrong configuration.

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

For configuration details, please see the following sections.