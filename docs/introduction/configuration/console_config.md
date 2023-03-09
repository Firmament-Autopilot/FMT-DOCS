
## Console Configuration

The console module is useful to print system information and provide command interaction. and can be redirected to difference devices. 

<img src="figures/console_out.png" width="30%">

`[console]` table is used to configure the console module, which contains one or more `[[console.devices]]` that the console can use with. The bellowing is a valid configuration with three devices reserved for console. They are *serial0*, *serial1* and *mav_console* respectively grouped by *[[console.devices]]* subtables. The first two are general serial devices, while the *mav_console* is a special one (virtual device), which provide read/write interface to transfer data with QGC Mavlink Console. 

The system will use the first device by default (serial0 in this case). And console can switch to other devices automatically when data has been received with `auto-switch` set to true.

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

The **type** has following choices:

- **serial**: the general serial device.

| Argument  | Type |  Description |
| ----------- | ------ | ----------- |
| name      | string | devie name       |
| baudrate | integer | serial baudrate       |
| auto-switch | bool | if true, automatically switch to device if data received        |

- **mavlink**: mavlink console device, which is able to send and receivce data via mavlink. So you can access the console via QGC mavlink console.

| Argument | Type  | Description |
| ----------- | ------- | ---------- |
| name      | string | devie name       |
| auto-switch | bool | if true, automatically switch to device if data received        |