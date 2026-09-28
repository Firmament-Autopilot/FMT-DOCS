# GNSS配置

GNSS用于配置飞控连接的GNSS设备，一个常规的配置如下所示

```
# GNSS Configuration
[gnss]
    [[gnss.devices]]
    id = 0
    protocol = "ublox"  # ublox or nmea
    type = "serial"
    name = "serial3"
    auto-config = true
    baudrate = 0        # 0 means auto detect baudrate
```

如下通过`[[gnss.devices]]`定义了一个GPS设备，其id为0，且使用的protocol为ublox协议。type和name用于定义通信链路的类型以及使用的设备名称，当前仅支持串口。

auto-config用来指定是否对GPS进行配置，因为有些GPS设备是不支持配置的。baudrate则是设定串口的波特率，如果设置为0则系统会去自动发现设备的波特率。

> 自动发现波特率仅针对支持配置的GPS设备

`[[gnss.devices]]`包含如下选项：

| Argument    | Type   | Description                                                  |
| ----------- | ------ | ------------------------------------------------------------ |
| id          | int    | gps id, which is useful if multiple gps connected            |
| protocol    | string | indicate which protocol is used, ublox or nmea               |
| type        | string | currently only serial is supported                           |
| name        | string | device name                                                  |
| auto-config | bool   | whether to configure gnss device                             |
| baudrate    | int    | the baudrate of serial device. set to 0 to auto-detect baudrate |
