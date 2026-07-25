# 以太网配置

s1飞控集成了以太网功能，`[ethernet]`用来对以太网进行配置，可以基于UDP配置多个类似串口的字符设备，用于数据的传输。

如下所示配置了一个以太网设备，设备名称是eth_dev0，local_addr为本机IP地址，为0.0.0.0则表示接收所有发送给本机的数据包，local_port用来设置本机的断开，为0则表示自动分配。remote_addr和remote_port则是用于配置远端（即数据接收端）的IP和端口。

```
# Ethernet Configuration
[ethernet]
    [[ethernet.devices]]
    name = "eth_dev0"
    local_addr = "0.0.0.0"
    local_port = 0
    remote_addr = "192.168.1.24"
    remote_port = 14550
```

我们也可以注册多个设备，如下所示：

```
# Ethernet Configuration
[ethernet]
    [[ethernet.devices]]
    name = "eth_dev0"
    local_addr = "0.0.0.0"
    local_port = 0
    remote_addr = "192.168.1.24"
    remote_port = 14550
    
    [[ethernet.devices]]
    name = "eth_dev1"
    local_addr = "0.0.0.0"
    local_port = 0
    remote_addr = "192.168.1.24"
    remote_port = 14551
    
    [[ethernet.devices]]
    name = "eth_dev2"
    local_addr = "192.168.1.30"
    local_port = 5000
    remote_addr = "192.168
```

`[[ethernet.devices]]`的选项包含如下：

| Argument    | Type     | Description                                                  |
| ----------- | -------- | ------------------------------------------------------------ |
| name        | string   | device name，which will be created by system                 |
| local_addr  | string   | optional, default is 0.0.0.0. UDP packet sent to this address will be accepted, 0.0.0.0 means accept all packets. |
| local_port  | interger | optinal, default is 0. The local port, 0 means let system assign a port. |
| remote_addr | string   | remote ip address                                            |
| remote_port | interger | remote port                                                  |

配置好以太网设备后，可以在mavproxy中来使用它，也可以在系统中类似串口设备那样，通过rt_device的接口来使用。

```
[mavproxy]
    [[mavproxy.devices]]
    chan = 0
    type = "eth"
    name = "eth_dev0"
```

