## Mavproxy Configuration

The mavproxy module implements mavlink protocol and it is responsible for mavlink data communication, including message sending and reception.

`[mavproxy]` table is used to configure the mavproxy module, which supports multiple channels (`currently 2 channels are supported, channel 0 for GCS and channel 1 for onboard computer`) and each channel can defines one or more `[[mavproxy.devices]]` that the mavproxy channel can use. The bellowing is a valid configuration with two devices reserved for channel0 and one device for channel1 . 

The system will use the first device by default for mavproxy channel and is able to switch to other devices if multiple devices defined for the channel. For instance, mavproxy channel0 can switch to usb0 if usb is connected. When usb disconnected, the mavproxy channel0 will switch back to serial1 device.

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
