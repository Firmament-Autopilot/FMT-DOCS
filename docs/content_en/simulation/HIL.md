
## Hardware-In-The-Loop (HIL) Simulation

Hardware-in-the-Loop (HIL) is the simulation mode that FMT firmware runs on a real flight controller (Such as ICF5 or Pixhawk). This approach has the benefit to test the performance of the actual flight code on the real hardware.

FMT supports the standard Mavlink protocol with HIL messages. Therefore FMT can support any HIL simulator which support Mavlink protocol.

<p align="center">
	<img src="./figures/hil_diag.png" width="40%">
</p>

### Setting Up HIL

HIL simulation is enabled by adding `#define FMT_USING_HIL` in *fmtconfig.h* at BSP folder. Then rebuild the system.

<p align="center">
	<img src="./figures/using_hil.png" width="20%">
</p>

When system is powered on, type `mcn list` command in console. You will find that sensor topic publich frequency are all 0, which is normal, because HIL simualtor (such as AirSim, Gazebo, etc) is not connected and the sensor data for HIL simulation mode comes from outside.

<p align="center">
	<img src="./figures/hil_data.png" width="25%">
</p>

### AirSim Simulation
FMT supports to simulate with AirSim. You need first download AirSim, either binaries or source. For more information about AirSim, please access https://microsoft.github.io/AirSim/.

Once you have AirSim installed, there will be a AirSim folder created at *User/Documents*.

<p align="center">
	<img src="./figures/airsim_folder.png" width="40%">
</p>

Open *settings.json* and change the content as follow. Notice that you need change the `SerialPort` to the flight controller's port which connected to your computer.

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

Then start the AirSim. The upper left corner shows that you are connected to the flight controller, and then you can control it.

<p align="center">
	<img src="./figures/airsim_play.png" width="50%">
</p>

### FMT-Sim Simulation
TBA

<p align="center">
	<img src="./figures/fmt_sim_mc.png" width="50%">
</p>

<p align="center">
	<img src="./figures/fmt_sim_fw.png" width="50%">
</p>