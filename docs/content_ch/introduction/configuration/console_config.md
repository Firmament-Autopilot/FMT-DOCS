
## 控制台配置

控制台可以用来打印系统信息和提供命令交互，其可以被重定向到不同的设备上。

<img src="figures/console_out.png" width="30%">

`[console]`可以被用来配置控制台设备，它包含了一个或者多个`[[console.devices]]`控制台可以使用的设备。如下所示的配置定义了三个控制台可以使用的设备，分别是*serial0*，*serial1*和*mav_console*，他们分别使用`[[console.devices]]`来定义。其中前两个是通用的串口设备，*mav_console*是一个比较特殊的设备（虚拟设备），它提供了和QGC的Mavlink Console接口的读写能力。

系统默认使用第一个设备（在这里是serial0）。如果其它设备设置了`auto-switch`未true，那么当对应设备收到数据后，控制台将自动切换到对应设备上。

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

**type**有如下选择:

- **serial**: 通用串口设备。

| Argument  | Type |  Description |
| ----------- | ------ | ----------- |
| name      | string | devie name       |
| baudrate | integer | serial baudrate       |
| auto-switch | bool | if true, automatically switch to device if data received        |

- **mavlink**: mavlink控制台设备。

| Argument | Type  | Description |
| ----------- | ------- | ---------- |
| name      | string | devie name       |
| auto-switch | bool | if true, automatically switch to device if data received        |