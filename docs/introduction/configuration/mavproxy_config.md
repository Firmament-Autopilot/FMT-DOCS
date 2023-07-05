## Mavproxy Configuration

The `mavproxy` module implements the MAVLink protocol and is responsible for handling MAVLink data communication, including message transmission and reception.

The configuration of the `mavproxy` module is defined in the `[mavproxy]` table. It supports multiple channels, where channel 0 is designated for the Ground Control Station (GCS) and channel 1 is designated for the Onboard Computer (OBC). Each channel can be associated with one or more `[[mavproxy.devices]]` that the corresponding `mavproxy` channel can utilize.

Here is an example configuration that includes two devices reserved for channel 0 and one device reserved for channel 1.

```
[mavproxy]
    [[mavproxy.devices]]        
    chan = 0					# channel 0 (GCS) device
    type = "serial"
    name = "serial1"            # device name
    baudrate = 57600            # serial baudrate

    [[mavproxy.devices]]        
    chan = 0					# channel 0 (GCS) device
    type = "usb"
    name = "usbd0"              # device name
    # automatically switch to usb if connected, switch back to default device is disconnected
    auto-switch = true
    
    [[mavproxy.devices]]
    chan = 1					# channel 1 (Onboard Computer) device
    type = "serial"
    name = "serial2"
    baudrate = 115200
```

By default, the system will utilize the first device defined for the `mavproxy` channel. However, if multiple devices are defined for a channel, the system has the capability to switch to other available devices. For instance, if a USB connection is established, `mavproxy` channel 0 can switch to the `usb0` device. If the USB connection is disconnected, `mavproxy` channel 0 will automatically switch back to the `serial1` device.



The **chan** has following choices:

- 0: Channel for ground control station.
- 1: Channel for onboard computer.

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
