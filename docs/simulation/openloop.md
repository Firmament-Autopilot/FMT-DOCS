
## Open-Loop Simulation

Open-Loop simulation is a vary useful way to analyze your software performance in the area of model-based design. The necessary model input data are logged by *Mlog* module, which works like a black box. Parse log data and feed it to model in simulation environment to run open-loop simulation, we can retrieve the output of model and all internal signal.

<img src="figures/openloop_diagram.png" width="50%">

## Setting Up Open-Loop Simulation

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
