## 空速校准

空速校准仅适用于配备空速传感器的飞行器，例如固定翼和垂直起降（VTOL）飞机。

在校准空速传感器之前，请确保您处于无风的室内环境中。

接下来，输入命令 `calib airspeed` 以启动校准过程。校准完成后，结果将显示出来，并存储在 `DIFF_PRESS_OFFSET` 参数中。要保存校准结果，请使用命令 `param save`。

```
msh />calib airspeed
diff pressure offset:-93.060173
you need enter param save calibration result
```