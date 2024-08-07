## Mavproxy配置 

`Mavproxy`模块实现了mavlink通信协议,负责处理 MAVLink 数据通信，包括消息的发送和接收。

`[mavproxy]`可以被用来配置mavproxy模块，它可以包含一个或者多个`[[mavproxy.devices]]`。如下是一个包含两个mavproxy设备的配置，分别是*serial1*和*usb0*。

系统默认使用第一个设备（在这里是serial1）。这里因为usb0的`auto-switch`为true，当usb连接的时候，mavproxy将自动切换到usb设备上。当usb断开连接，mavproxy将切回到serial1设备。

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

    [[mavproxy.devices]]
    chan = 1                    # channel 1 (Onboard Computer) device
    type = "serial"
    name = "serial2"
    baudrate = 115200
```

默认情况下，系统将使用为 mavproxy 通道定义的第一个设备。但是，如果为一个通道定义了多个设备，系统可以切换到其他可用设备。如以上例子`usb`设备的 `auto-switch`参数设定为`true`，代表着如果建立了 USB 连接，mavproxy 通道 0 可以切换到 usb0 设备。如果 USB 连接断开，mavproxy 通道 0 将自动切换回 serial1 设备。

**chan**的值有如下两种选择:

- 0: 地面控制站的通道。
- 1: 车载计算机的通道。

**type** 包含如下选项:

- **serial**: 通用串口设备.

| Argument    | Type    | Description                                              |
| ----------- | ------- | -------------------------------------------------------- |
| name        | string  | devie name                                               |
| baudrate    | integer | serial baudrate                                          |

- **usb**: USB虚拟串口设备.

| Argument    | Type   | Description                                              |
| ----------- | ------ | -------------------------------------------------------- |
| name        | string | devie name                                               |
| auto-switch | bool   | if true, automatically switch to device if connected |
