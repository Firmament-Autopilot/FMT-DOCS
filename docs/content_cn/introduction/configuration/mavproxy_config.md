## 通信配置 

Mavproxy模块实现了mavlink通信协议,负责处理 MAVLink 数据通信，包括消息的发送和接收。

`[mavproxy]`可以被用来配置mavproxy模块，它可以包含多个`[[mavproxy.devices]]`设备。如下所示，我们配置了两个通道，chan 0和chan 1，分别对应地面站Mavlink链路和机载电脑Mavlink链路，每个链路可以定义一个或多个设备。

默认情况下，系统将使用为 mavproxy 通道定义的第一个设备。但是，如果为一个通道定义了多个设备，系统可以切换到其他可用设备。如下例子`usb`设备的 `auto-switch`参数设定为`true`，代表着如果建立了 USB 连接，mavproxy 通道 0 可以切换到 usb0 设备。如果 USB 连接断开，mavproxy 通道 0 将自动切换回 serial1 设备。

```
# Mavproxy Device Configuration
[mavproxy]
    [[mavproxy.devices]]
    chan = 0
    type = "serial"
    name = "serial1"
    baudrate = 57600

    [[mavproxy.devices]]
    chan = 0
    type = "usb"
    name = "usbd0"
    auto-switch = true

    [[mavproxy.devices]]
    chan = 1
    type = "serial"
    name = "serial2"
    baudrate = 115200
```

**chan**的值有如下两种选择:

- 0: 地面控制站的通道。
- 1: 机载计算机的通道。

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

- **eth**: 以太网设备.

| Argument | Type   | Description |
| -------- | ------ | ----------- |
| name     | string | devie name  |

---

### 转发RTCM消息

`[mavproxy]`可以可以包含一个或者多个`[[mavproxy.rtcm_devices]]`，该设备用来将来自地面站链路的RTK基站的RTK信号转发给rtcm_devices所配置的设备

```
    [[mavproxy.rtcm_devices]]
    type = "serial"
    name = "serial3" 
    baudrate = 115200
```

> 这里可以指定baudrate，如果不指定，则沿用当前串口设备默认的波特率
