
## Model-In-The-Loop (MIL) Simulation

Model-In-The-Loop is the simulation in an early development phase of modeling in the area of model-based design. MIL is a cheap way to test algorithms. In order to function properly, the algorithms must be simulated before deployment to real hardware to detect any serious bug which could result in catastrophic consequences.

Model-In-The-Loop simulation is a method in which you model is developed and executed in a simulation environment such as Simulink. A typical model structure for autopilot system is shown blow.

<img src="figures/mil_model.png" width="50%">

## Setting Up MIL Simulation

1. Open FMT-Model in Matlab (version 2018b or higher).
2. Double click **FMT_Model.prj** to open and initialize the project.
3. Open simulation model `simulation/MILSIM.slx`.
4. Select source of rc input. Right click RC block and select **Variant->Label Model Active Choice**.

    - Joystick: RC input from a joystick.
    - Mavlink: RC input is sent from the hardware via mavlink.

5. Open visualization block `MILSIM/Virtualization/3D_Visualization/Matlab_3D/Visualization/VR Sink`. Set viewpoint to *Isometric - No Rotation*.
6. Click **Run** button to start simulation.
7. When simulation finish, open *Simulation Data Inspector* to inspect simulation data.

<img src="figures/mil_sdi.png" width="50%">

## Visualization

The plant model generates vehicle states such as attitude, position, etc. These information can be sent to any visualization software, such as Flightgear, Gazebo, Webots, etc.

<img src="figures/matlab_3D.png" width="40%">
<img src="figures/flightgear.png" width="40%">
