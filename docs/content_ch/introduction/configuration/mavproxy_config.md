## Mavproxy配置 

Mavproxy模块实现了mavlink通信协议来负责mavlink数据的通信，包含消息的发送和接收。

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
```

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
