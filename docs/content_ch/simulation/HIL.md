
## 硬件在环（HIL）仿真

硬件在环（HIL）是指FMT固件在真实飞行控制器（如ICF5或Pixhawk）上运行的仿真模式。这种方法的好处是可以测试实际飞行代码在真实硬件上的性能。

FMT支持带有HIL消息的标准Mavlink协议。因此，FMT可以支持任何支持Mavlink协议的HIL模拟器。

<p align="center">
	<img src="./figures/hil_diag.png" width="40%">
</p>

### 设置HIL仿真

通过在BSP文件夹中的*fmtconfig.h*文件中添加`#define FMT_USING_HIL`来启用HIL仿真。然后重新构建系统。

<p align="center">
	<img src="./figures/using_hil.png" width="30%">
</p>

当系统上电后，在控制台中输入`mcn list`命令。您会发现传感器主题的发布频率都是0，这是正常的，因为HIL模拟器（例如AirSim、Gazebo等）尚未连接，HIL仿真模式下的传感器数据来自外部。

<p align="center">
	<img src="./figures/hil_data.png" width="35%">
</p>

### AirSim仿真

FMT支持与AirSim进行仿真。您需要首先下载AirSim，可以选择二进制文件或源代码。有关AirSim的更多信息，请访问 [AirSim官方网站](https://microsoft.github.io/AirSim/)。

安装AirSim后，*User/Documents*目录下会创建一个AirSim文件夹。

<p align="center">
	<img src="./figures/airsim_folder.png" width="50%">
</p>


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
	<img src="./figures/airsim_play.png" width="45%">
</p>

### FMT-Sim Simulation
待补充

<p align="center">
	<img src="./figures/fmt_sim_mc.png" width="45%">
</p>

<p align="center">
	<img src="./figures/fmt_sim_fw.png" width="45%">
</p>