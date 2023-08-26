## Mission Planning

This section provides information about how to create missions that will run when the vehicle switched to Mission mode.

### Create Mission

The mission is created via Ground Control Station. You can use QGC to plan a mission with waypoints and events (takeoff, land, return).

 <p align="center">
 	<img src="./figures/plan_mission.png" width="60%">
 </p>

After making the mission, click the **Upload** button. This will create a `/sys/mission.txt` file on the flight controller.

```
msh />ls sys
sih_param.xml  mission.txt
```

> Please note that currently the fixwing vehicle doesn't support takeoff and land event.

The flying speed is determined by the `CRUISE_SPEED` parameter, which you can modify according to your need.

The home position is set at the location where the vehicle takeoff.

### Start Mission

To start the mission, begin by switching the mode to Mission mode. Following that, issue either the **PreArm** or **Takeoff** command. You also have the option to simply slide the `Start Mission` button. This action will automatically switch the mode to Mission and trigger the PreArm command.

Keep in mind that you can change the mode using the Ground Control Station (GCS) only when the Remote Control (RC) is not connected. This is because the RC takes precedence over the GCS in mode selection. So, when the RC is active, the mode will follow the RC mode.

### Pause/Resume Mission

You can pause the ongoing mission at any point by either activating the **Pause** command or by changing the mode to **Hold** through the Ground Control Station (GCS).

To resume the current mission, simply switch the mode back to Mission or use the **Continue Mission** button.

 <p align="center">
 	<img src="./figures/mission_pause.png" width="60%">
 </p>

### Delete Mission

For mission deletion, you can utilize the QGroundControl (QGC) Mission -> File section to remove the mission, or you can use the command `rm /sys/mission.txt` to delete the mission file.

 <p align="center">
 	<img src="./figures/delete_mission.png" width="60%">
 </p>
