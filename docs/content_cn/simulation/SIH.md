
#### 纯硬件仿真 

纯硬件仿真 (Simulator-in-hardware, SIH) 是硬件在环仿真 (HIL) 的一种替代方案。在该配置中，除了飞控的软件代码以外，被控对象模型（包含动力学、传感器建模等）也同样运行在飞控硬件平台。相比于传统的硬件在环仿真，纯硬件仿真无需外部仿真主机，飞控本身就充当了仿真主机的角色。

SIH 相比 HIL 具有以下优点:

- 不需要单独配备价格昂贵的仿真主机，飞控本身就充当了仿真主机的角色，节省了设备成本。
- 整个仿真运行在同一套软件系统和硬件中，简化不同系统之间的时间同步问题，同时更容易将开发者的被控模型部署到仿真平台中。
- 操作简介方便，无需连接大量外部设备以及复杂的接线，省去了与外部数据传输的延时，具有更高的实时性。

<p align="center">
	<img src="content_cn/simulation/figures/sih_diag.png" width="45%">
</p>

#### 编译下载仿真固件

要开启SIH仿真，只需在编译固件时添加`--sim=SIH`的仿真选项，然后将编译好的固件下载到目标飞控硬件平台。

```
scons -j16 --sim=SIH
```


开启SIH仿真后，系统的启动信息会包含**Plant Model**信息，这表示SIH仿真模式已开启，并且被控对象模型Plant已经在飞控上平台上运行。

```
   _____                               __ 
  / __(_)_____ _  ___ ___ _  ___ ___  / /_
 / _// / __/  ' \/ _ `/  ' \/ -_) _ \/ __/
/_/ /_/_/ /_/_/_/\_,_/_/_/_/\__/_//_/\__/ 
Firmware....................FMT FMU v0.1.0
Kernel....................RT-Thread v4.0.3
RAM.................................512 KB
Target......................Pixhawk4 FMUv5
Vehicle.........................Quadcopter
INS Model..................Base INS v0.1.0
FMS Model..................Base FMS v0.1.0
Control Model.......Base Controller v0.1.0
Plant Model.............Multicopter v0.1.0
Task Initialize:
  comm..................................OK
  logger................................OK
  fmtio.................................OK
  status................................OK
  vehicle...............................OK
[559] I/StatusTask: SIH Simulation
```

#### 连接外部视景系统

SIH仿真可以连接外部视景系统如FMT-SIM。修改FMT-SIM的`settings.jason`配置文件，将`SimMode`设置为SIH，并正确配置MavproxyFCS与飞控连接。

```
{
	"Map": "MainMenu",
	"Vehicles": [
		{
			"VehicleType": "Multicopter",
			"ID": 1,
			"SimMode": "SIH",
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

> 注意：需先断开地面站与飞控的连接，才能建立飞控与FMT-SIM的连接。FMT-SIM连接上飞控后，会自动建立飞控与地面站的连接。
