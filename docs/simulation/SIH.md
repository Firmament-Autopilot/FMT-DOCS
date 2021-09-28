
## Simulation-In-Hardware (SIH)

Simulation-In-Hardware (SIH) is an alternative to Hardware-In-The-Loop simulation (HITL). In this setup, everything is running on embedded hardware - the controller, the inertial navigation system, the flight management system and the simulator (plant). The Desktop computer is only used to display the virtual vehicle.

The SIH provides two benefits over the HITL:

- It ensures synchronous timing by avoiding the bidirectional connection to the computer. As a result the user does not need such a powerful desktop computer.

- The whole simulation remains inside the FMT environment. Developers can more easily incorporate their own mathematical model into the simulator. They can, for instance, modify the aerodynamic model, or noise level of the sensors, or even add a sensor to be simulated.

## Setting Up SIH

SIH simulation is enabled by adding `#define FMT_USING_SIH` in *fmtconfig.h*. Then rebuild the system.

When system is powered on, the system banner would list **Plant Model** information.

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