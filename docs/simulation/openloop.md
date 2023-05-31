
## Data Simulation

Data simulation is an extremely useful method for evaluating the performance of your algorithm or model using the model-based design approach. The Mlog module logs the required input data for the model, acting as a black box. By parsing the logged data and providing it as input to the model in a simulation environment, we can perform data simulation and retrieve both the output of the model and all internal signals.

<img src="figures/openloop_diagram.png" width="50%">

Since the model runs from boot, it is necessary to record the log data from the boot stage. The MLOG_MODE parameter determines when to start and stop logging.

```c
    /* Determines when to start and stop logging.
	0: disabled
	1: when armed until disarm
	2: from boot until disarm
	3: from boot until shutdown  */
    PARAM_INT32(MLOG_MODE, 0),
```

To initiate logging from boot, simply enter the following command in the console. Keep in mind that configuring the MLOG_MODE to 2 will commence logging at boot and cease when the vehicle is disarmed. 

```shell
param set MLOG_MODE 2
param save
reboot
```

 If you intend to perform multiple tests within a single log, such as arming and disarming the system multiple times, you should set the MLOG_MODE to 3. This configuration will enable logging to continue until the system shuts down or until logging is manually stopped.

## Setting Up Data Simulation

1. Enable Mlog to record data from boot (Set parameter MLOG_MODE to 2 or 3).
2. Do your flight test.
3. Stop Mlog and get log file.
4. Use [script](https://github.com/Firmament-Autopilot/FMT-Model/blob/master/utils/log_parser/parse_mlog.m) to parse log and generate *.mat* files.
5. Open and initialize FMT-Model project.
6. Load *.mat into Matlab and execute `load_parameter.m`.
7. Open simulation model `simulation/OpenloopSIM.slx`.
8. Click **Run** button to start simulation.

## Data Visualization

You can use the Simulation Data Inspector (SDI) to visualize the data you generate throughout the simulation process. Or you can use matlab to plot any data you want.

Here is an example using SDI. You can see that the simulation results (red lines) are completely matched with recorded actual results (blue lines).

<img src="figures/sdi.png" width="50%">
