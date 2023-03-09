## 输出配置

`[actuator]`可以被用来配置actuator输出模块，它包含了一个或多个`[[actuator.devices]]`。如下是一个包含了两个输出设备*main_out*和*aux_out*的配置。对于像Pixhawk标准的硬件，包含了2个pwm输出端口（8个主输出口，8个辅助输出口）。不同的输出设备可以分别设置PWM的输出频率。

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

**protocol** 包含如下选项：

- **pwm**: 输出PWM信号.

| Argument  | Type |  Description |
| ----------- | ------ | ----------- |
| name      | string | devie name       |
| freq | integer | pwm signal frequency, range in [50, 400] Hz |

- **dshot**: 暂不支持

### 映射

`[[actuator.mappings]]`可被用来定义映射。映射可以是从遥控通道或者控制器输出到任意的输出设备。您可以定义任意数目的映射。

如下是一个例子。第一个映射将控制器输出的1,2,3,4通道映射到主输出main_out的1,2,3,4通道。第二个映射将遥控的通道2映射到辅助输出aux_out的1,2,3,4通道。

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