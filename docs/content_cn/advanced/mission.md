## 任务规划

本节提供有关如何创建任务的信息，这些任务将在车辆切换到任务模式时运行。

### 创建任务

任务通过地面控制站创建。您可以使用 QGC 规划带有航点和事件（起飞、降落、返航）的任务。

<p align="center">
    <img src="./figures/plan_mission.png" width="40%">
</p>

完成任务规划后，请点击 **上传** 按钮。这将在飞行控制器上创建一个 `/sys/mission.txt` 文件。

```
msh />ls sys
sih_param.xml  mission.txt
```

> 请注意，目前固定翼飞行器不支持起飞和降落事件。

飞行速度由 `CRUISE_SPEED` 参数决定，您可以根据需要进行修改。

返航点设置为飞行器起飞的位置。

### 启动任务

要启动任务，请首先将模式切换到任务模式。然后，可以执行 **PreArm** 或 **Takeoff** 命令。您也可以直接滑动 `启动任务` 按钮。这将自动切换到任务模式并触发 PreArm 命令。

请注意，您只能在遥控器（RC）未连接时通过地面控制站（GCS）更改模式。因为遥控器优先于地面控制站在模式选择上。因此，当遥控器处于活动状态时，模式将遵循遥控器模式。

### 暂停/恢复任务

您可以通过激活 **暂停** 命令或通过地面控制站（GCS）将模式更改为 **Hold** 来在任何时候暂停正在进行的任务。

要恢复当前的任务，请简单地将模式切换回任务模式或使用 **继续任务** 按钮。

<p align="center">
    <img src="./figures/mission_pause.png" width="40%">
</p>

### 删除任务

要删除任务，您可以使用 QGroundControl（QGC）中的任务 -> 文件部分删除任务，或者可以使用命令 `rm /sys/mission.txt` 来删除任务文件。

<p align="center">
    <img src="./figures/delete_mission.png" width="40%">
</p>
