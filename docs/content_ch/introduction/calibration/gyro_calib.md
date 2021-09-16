
## 进行校准

陀螺仪校准是通过QGroundControl进行。遵从QGroundControl的指导完成校准流程。

1. 将机体放置水平并保持不动，然后点击**Gyroscope**传感器按钮。

<img src="figures/gyro_calib1.png" width="50%">

2. 点击OK开始校准。顶部的进度条将显示校准进度。

<img src="figures/gyro_calib2.png" width="50%">

3. 完成后，QGroundControl将显示进度条*Calibration complete*。

<img src="figures/gyro_calib3.png" width="50%">

## 查看&保存结果

1. 校准的结果可以通过在控制台输入`param list CALIB`指令查看

```
msh />param list CALIB
CALIB:
      GYRO0_XOFF: -0.024290
      GYRO0_YOFF: -0.059920
      GYRO0_ZOFF: -0.007062
......
```

2. 控制台输入`param save`保存校准结果，否则系统断电将丢失未保存的校准结果。
