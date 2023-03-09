## Mavproxy Configuration

The mavproxy module implements mavlink protocol and it is responsible for mavlink data communication, including message sending and reception.

`[mavproxy]` table is used to configure the mavproxy module, which contains one or more `[[mavproxy.devices]]` that the mavproxy can use. The bellowing is a valid configuration with two devices reserved for mavproxy. They are *serial1*, and *usb0* respectively grouped by *[[console.devices]]* subtables. 

The system will use the first device by default (serial1 in this case) and mavproxy can switch to usb0 if usb is connected. When usb disconnected, the mavproxy will switch back to serial1 device.

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

The **type** has following choices:

- **serial**: the general serial device.

| Argument    | Type    | Description                                              |
| ----------- | ------- | -------------------------------------------------------- |
| name        | string  | devie name                                               |
| baudrate    | integer | serial baudrate                                          |

- **usb**: the usb cdc device.

| Argument    | Type   | Description                                              |
| ----------- | ------ | -------------------------------------------------------- |
| name        | string | devie name                                               |
| auto-switch | bool   | if true, automatically switch to device if connected |
