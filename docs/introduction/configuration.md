
## System Configuration

FMT system is fully configurable with [toml](https://toml.io/en/) file, which gives you great flexibility to tune the system as you want. On boot process, the system will try to load the configuration file from `/sys/sysconfig.toml`. If this file does not exist, the default system configuration is used， which only contains very limited functions (rc and actuator are disabled). A default *sysconfig.toml* is provided in each target folder. You need upload this file to `/sys` folder on board before the systen become fully functional.

E.g. This is the default [sysconfig.toml](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v5/sysconfig.toml) for pixhawk FMUv5. Be noticed the target value should match with BSP's target name (defined in *bsp.h*). Otherwise the system won't boot because of the assertion failue to prevent using of a wrong configuration.

## Console Configuration

`[console]` table is used to configure the console module. The belowing is a valid console configuration with three device reserved for console. They are `serial0`, `serial1` and `mav_console` grouped by `[[console.devices]]` tables. The first two are general serial devices, while the mav_console is a special one (virtual device), which provide read/write interface to transfer data with QGroundControl mavlink console. On system boot stage, the console will use the first device by default (serial0 in this case). And console can switch to other devices automatically when data has been received with `auto-switch` set to true.

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

## Mavproxy Configuration

`[mavproxy]` table is used to configure the mavproxy module, which implements mavlink protocol to communicate with ground station. Similar to console configuration, tables `[[mavproxy.devices]]` specify which devices are reserved for mavproxy. The first device is used by defdault.

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

## Pilot CMD (RC) Configuration

`[pilot-cmd]` table is used to configure the pilot_cmd (rc) module. You can configure the rc device, protocol, channel number, sample time, channel ranges, etc in `[pilot-cmd.device]` table. The stick channel is configured by `stick-channel`, which is an array with 4 elements represents to the channel value for left-stick left/right, left-stick up/down, right-stick left/right and right-stick up/down. A typical pilot-cmd device configuration with stick-channel is shown below:

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

Pilot control mode can be defined with `[[pilot-cmd.mode]]`. e.g. to define the stabilize mode:

```
    [[pilot-cmd.mode]]
    mode = 3                    # stabilize mode
    channel = 5
    range = [1800,2000]
```

If rc channel 5 value is in the range of [1800, 2000], the stabilize mode will be selected. 

It is also possible to map multiple rc channels to a single mode, which provides more mode choices with limited rc channels.

```
    [[pilot-cmd.mode]]
    mode = 3                    # stabilize mode
    channel = [5,6]
    range = [[1800,2000],[1400,1600]]
```

You can also configure the pilot command. There are two types of command supported:

- *cmd1*: event command， valid only for short period, like a pulse signal.
- *cmd2*: status command, valid all the time until new status command arrived, like a step signal.

The pilot command can be defined with `[[pilot-cmd.command]]` tables, e.g.

```
    [[pilot-cmd.command]]
    type = 1                    # 1:event | 2:status
    cmd = 1002                  # force-disarm: forcely disarm motors for safety concern
    channel = 6                 # command channel
    range = [1800,2000]         # if channel value in this range, the event is triggered

    [[pilot-cmd.command]]
    type = 2                            # 1:event | 2:status
    cmd = 2001                          # for testing
    channel = [4, 5]                    # command channel
    range = [[1300,1500],[1200,1400]]   # if channel value in this range, the event is triggered
```

## Actuator Configuration

`[actuator]` table is used to configure the actuator devices. `[[actuator.devices]]` tables defines which actuator devices are available. For pixhawk there are two actuator output (main_out port and aux_out port), so a typical configuration is shown below. You can modify pwm frequency, the support frequency range is between 50Hz to 400Hz.

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

`[[actuator.mappings]]` tables can be used to map rc channels or controller output to any actuator's channel. Here is an example. The first mapping maps controller output channel of 1,2,3,4 to main_out actuator channel of 1,2,3,4. The second mapping maps rc channel 2 to aux_out actuator channel of 2,3,4.

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
