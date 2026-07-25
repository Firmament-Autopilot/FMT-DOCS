## 输出配置

`[actuator]`可以被用来配置actuator输出模块，它包含了一个或多个`[[actuator.devices]]`。

对于S1飞控来说，有2个输出设备，main_out和aux_out，分别对应M1-M12的PWM输出以及A1-A4的PWM输出。不同的输出设备可以分别设置PWM的输出频率。

比如如下所示，将main_out设置为了400Hz频率的PWM，将aux_out设置为了50Hz频率的PWM。这样可以使用aux_out连接要低PWM频率的设备，如舵机，用main_out连接高PWM频率的设备，如电机。

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

```
    [[actuator.devices]]
    protocol = "pwm"            # pwm or dshot
    name = "main_out"           # device name
    freq = 400                  # pwm frequency in Hz
```

| Argument  | Type |  Description |
| ----------- | ------ | ----------- |
| name      | string | devie name       |
| freq | integer | pwm signal frequency, range in [50, 400] Hz |
| init-val | [chan, val] | init value for pwm channel. e.g, set pwm channel 5,6 init value to 1000: [[5, 6], [1000, 1000]] |



- **dshot**: 输出DSHOT信号. 注意，当前不是所有的飞控都支持该协议，查看对应飞控的BSP说明看是否支持DSHOT协议

```
    [[actuator.devices]]
    protocol = "dshot"
    name = "main_out"
    speed = 600                 # dshot speed: 150, 300, 600
    telem-req = false           # request telemetry on main_out
```

| Argument  | Type    | Description                         |
| --------- | ------- | ----------------------------------- |
| name      | string  | devie name                          |
| speed     | integer | dshot speed, can be 150, 300 or 600 |
| telem-req | bool    | request telemetry or not            |

### 映射

`[[actuator.mappings]]`可被用来定义映射。映射可以是从遥控通道或者控制器输出到任意的输出设备。您可以定义任意数目的映射。

如下是一个例子。第一个映射将控制器输出的1,2,3,4通道映射到主输出main_out的1,2,3,4通道。

```
[[actuator.mappings]]
from = "control_out"
to = "main_out"
chan-map = [[1,2,3,4],[1,2,3,4]]
```

也可以直接将遥控器的通道映射到PWM输出，如下所示是将遥控的通道2映射到辅助输出main_out的2,3,4通道。

```
[[actuator.mappings]]
from = "rc_channels"
to = "main_out"
chan-map = [[2,2,2],[2,3,4]]
```

### 初始值

PWM通道的输出值默认是1000，这适用于电机控制场景，但是对于舵机控制可能就不试用。可以通过增加`init-val`选项来设置对应pwm输出通道的初始值。

```
    [[actuator.devices]]
    protocol = "pwm"            # pwm or dshot
    name = "main_out"           # device name
    freq = 400                  # pwm frequency in Hz
    init-val = [[1,5,9,7,3],[1500,1600,1700,1800,1100]]
```

