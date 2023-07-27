
## Simulation-In-Hardware (SIH)

Simulation-In-Hardware (SIH) is an alternative to Hardware-In-The-Loop simulation (HITL). In this setup, everything is running on embedded hardware - the controller, the inertial navigation system, the flight management system and the simulator (plant). The Desktop computer is only used to display the virtual vehicle.

In HIL simulations, an external machine is generally required to run the Plant model. However, this is not required for SIH simulation, because Plant runs directly in the flight controller. SIH can be used to familiarize yourself with the operation before flying the real drone.

<p align="center">
	<img src="./figures/sih_diag.png" width="40%">
</p>

### Setting Up SIH

SIH simulation is enabled by adding `#define FMT_USING_SIH` in *fmtconfig.h* at BSP folder. Then rebuild the system.

<p align="center">
	<img src="./figures/using_sih.png" width="20%">
</p>

When system is powered on, the system banner would output **Plant Model** information, which means the SIH simulation mode is activated. Then, you can connect the GCS and RC to operate the SIH simulation just like operating a real aircraft.

<p align="center">
	<img src="./figures/sih_banner.png" width="20%">
</p>

### SIH Simulation with Multicopter
FMT has a built-in multicopter plant model, so you can use SIH to simulate with multicopter. Make sure `#define FMT_USING_SIH` is uncommented. Then use the following command to build the firmware.

```
scons -j4
```

> The default vehicle is Multicopter, so you do not need add --vehicle=Multicopter explicitly.

Connect flight controller to QGC via usb. You can use RC, ground control station, or onboard computer to control the flight controller just like a real aircraft.

### SIH Simulation with Fixwing
FMT has a built-in fixwing plant model, so you can use SIH to simulate with multicopter. Make sure `#define FMT_USING_SIH` is uncommented. Then use the following command to build the firmware.

```
scons -j4 --vehicle=Fixwing
```

Connect flight controller to QGC via usb. You can use RC, ground control station, or onboard computer to control the flight controller just like a real aircraft.