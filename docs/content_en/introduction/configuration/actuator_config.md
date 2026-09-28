## Actuator Configuration

`[actuator]` table is used to configure the actuator module, which contains one or more`[[actuator.devices]]` subtables to defines the available actuator controller. The belowing is a configuration that defines two actuator devices *main_out* and *aux_out*. For pixhawk standard hardware, which usually has two pwm outputs (8 main out, 8 aux out). The pwm frequency pf the two devices can be configured seperately.

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

The **protocol** has following choices:

- **pwm**: actuator outputs pwm signal.

| Argument  | Type |  Description |
| ----------- | ------ | ----------- |
| name      | string | devie name       |
| freq | integer | pwm signal frequency, range in [50, 400] Hz |

- **dshot**: currently is not supported yet

### Mapping

`[[actuator.mappings]]` subtables can be used to map rc channels or controller output to any actuator driver's channel. You can define one or more mappings. 

Here is an example. The first mapping maps controller output channel of 1,2,3,4 to main_out actuator channel of 1,2,3,4. The second mapping maps rc channel 2 to aux_out actuator channel of 2,3,4.

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