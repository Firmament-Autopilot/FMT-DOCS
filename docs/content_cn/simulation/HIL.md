
#### 硬件在环仿真

硬件在环仿真（Hardware-in-loop Simulation，HIL）是指FMT飞控固件在真实飞控硬件平台，而被控对象模型（如动力学、传感器建模等）运行在单独的仿真主机的仿真手段。HIL仿真结果是最接近真实工程的仿真手段，因为飞控软件运行在真实硬件平台，除了算法，嵌入式软件以及硬件也能进行仿真和验证。

FMT支持HIL仿真模式，其通过标准Mavlink的HIL消息来跟外部仿真主机进行通信。

<p align="center">
	<img src="content_cn/simulation/figures/hil_diag.png" width="40%">
</p>

#### 编译下载仿真固件

要开启HIL仿真，只需在编译固件时添加`--sim=HIL`的仿真选项，然后将编译好的固件下载到目标飞控硬件平台。

```
scons -j16 --sim=HIL
```


下载HIL仿真固件后，在飞控控制台中输入`mcn list`命令，会发现传感器主题的发布频率都是0，这是正常的，因为当前为HIL仿真模式，其传感器数据需连接外部仿真主机获得。

<p align="center">
	<img src="content_cn/simulation/figures/hil_data.png" width="35%">
</p>

#### 连接外部仿真机

准备一台PC主机，运行FMT官方的仿真平台FMT-SIM，也可以使用其它支持Mavlink的第三方仿真平台，如AirSim。

FMT默认使用USB与外部仿真主机通信，将飞控通过与仿真机连接即可。也可以使用其它通信链路，如串口、网口等，具体请参考**通信配置**章节。

##### FMT-SIM

FMT-SIM是FMT官方团队基于UE虚幻引擎开发的视景仿真系统。可以关注`FMT智驾仪`公众号，并在后台回复**S1**获取。

<p align="center">
	<img src="content_cn/simulation/figures/fmt_sim_mc.png" width="45%">
</p>

修改FMT-SIM的`setting.jason`配置文件，将SimMode修改为HIL，并且正确配置MavproxyFCS与飞控进行连接，如下所示。

```
{
	"Map": "MainMenu",
	"Vehicles": [
		{
			"VehicleType": "Multicopter",
			"ID": 1,
			"SimMode": "HIL",
			"MoveScale": 1.0,
			"SpawnPos": {
				"X": 0, "Y": 0, "Z": 0
			},
			"SpawnRot": {
				"Roll": 0, "Pitch": 0, "Yaw":0
			},
			"MavproxyFCS": {
				"Interface": "Serial",
				"SerialPort": 21,
				"SerialBaudRate": 115200,
				"LocalIP": "127.0.0.1",
				"LocalPort": 14553,
				"RemoteIP": "0.0.0.0",
				"RemotePort": 0,
				"Autobound": true		
			},
			"MavproxyGCS": {
				"Interface": "UDP",
				"LocalIP": "127.0.0.1",
				"LocalPort": 0,
				"RemoteIP": "127.0.0.1",
				"RemotePort": 14550,
				"Autobound": false		
			}
		}
	]
}
```



##### AirSim

FMT支持AirSim等第三方的视景平台进行仿真。您需要首先下载AirSim，可以选择二进制文件或源代码。有关AirSim的更多信息，请访问 [AirSim官方网站](https://microsoft.github.io/AirSim/)。

安装AirSim后，*User/Documents*目录下会创建一个AirSim文件夹。

打开settings.json文件并将内容更改如下。请注意，您需要将SerialPort更改为连接到您计算机的飞行控制器的端口。

```json
{
	"SettingsVersion": 1.2,
	"SimMode": "Multirotor",
	"ClockType": "SteppableClock",
	"Vehicles": {
		"PX4": {
			"VehicleType": "PX4Multirotor",
			"UseSerial": true,
			"UseTcp": false,
			"SerialPort": "COM9",
			"SerialBaudRate": 57600,
			"Parameters": {
				"ROLL_RATE_P": 0.02,
				"PITCH_RATE_P": 0.02,
				"ROLL_RATE_I": 0.05,
				"PITCH_RATE_I": 0.05,
				"ROLL_RATE_D": 0.0005,
				"PITCH_RATE_D": 0.0005
			}
		}
	}
}
```

然后启动AirSim。左上角会显示您已连接到飞行控制器，然后您可以进行控制。

<p align="center">
	<img src="content_cn/simulation/figures/airsim_play.png" width="45%">
</p>
