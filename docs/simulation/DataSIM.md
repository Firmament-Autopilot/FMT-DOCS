
## Data Simulation

Data simulation is an extremely useful method for evaluating the performance of your algorithm model using the model-based design approach. The [MLog](../module/mlog.md) module logs the required input data for the model, acting as a black box. By parsing the logged data and providing it as input to the model in a simulation environment, we can perform data simulation and retrieve both the output of the model and all internal signals.

<img src="figures/openloop_diagram.png" width="50%">

### Record Log Data

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

> You can also change value of MLOG_MODE by QGC. Remember to enter `param save` after modifying the parameter values.

 If you intend to perform multiple tests within a single log, such as arming and disarming the system multiple times, you should set the MLOG_MODE to 3. This configuration will enable logging to continue until the system shuts down or until logging is manually stopped.

### Download Log Data

Log data is recorded in the current session folder. Each time when system power up the current session ID is incresed by 1 is. When the id hits the maximum value (usually 20), it will start at 1 again, which will overwrite the old session folder.

To check the current session id, you can enter `boot_log`.

<img src="figures/session_id.png" width="30%">

You can then download the logs through QGC's Component->Onboard Files (QGC 3.5.6 Only) or using a sd card reader.

<img src="figures/download_log.png" width="40%">

### Parse Log Data
FMT-Model provides a matlab [script](https://github.com/Firmament-Autopilot/FMT-Model/blob/master/utils/log_parser/parse_mlog.m) to parse the log data. Run the script in Matlab and select the log file, which will parse log data and generate *.mat* files.

<img src="figures/parsed_log.png" width="40%">

### Load Log Data
Drag all *.mat* files to Matlab. Then run `load_parameter.m` and `mission_data_preprocess.m` to preprocess the log data.

### Run Data Simulation
Now you can run the data simulation. Please make sure you have initialized FMT-Model by double-click `FMT_Model.prj`.

Open data simulation model `simulation/DataSIM.slx` and click **Run** button to start simulation.

<img src="figures/data_sim.png" width="80%">

### Data Inspect

When simulation finish, you could click the *CompareModelOut* button to plot the simulation model output data (red) compared with real data (blue).

<img src="figures/compare_model_out.png" width="60%">

Normally, the simulation data should be the same as the real data. If this is not the case, there may be the following reasons:

- The log data is not recorded from boot. To solve this, you can set MLOG_MODE to 2 or 3 to automatically start logging when system powered on.
- The simulation model is not the same as the one run in the flight controller. To solve this, you can rebuild the model and generate the code.
- There may be data loss in the logs. When log is stopped,, you can check whether the value of lost is 0.

<img src="figures/log_lost.png" width="50%">

### Check More Data

You can use Data Simulation to retrive all data of model. You can select a data signal that you want check, give it a name and **Enable Data Logging** (a signal flag will apear). 

<img src="figures/enable_data_log.png" width="70%">

Then run the simulation again, You can use the Simulation Data Inspector (SDI) to visualize the data.

<img src="figures/sdi_signal.png" width="70%">
