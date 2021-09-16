
## 进行校准

加速度校准通过QGroundControl进行。遵循QGroundControl的指导完成校准流程。

1. 点击**Accelerometer**传感器按钮。

<img src="figures/accel_calib1.png" width="50%">

2. 点击OK开始校准。
3. 将机体置于屏幕图片所示方向。一旦黄色框出现，将机体静置。当前方向校准完成后屏幕上对应图标的框将变绿。

<img src="figures/accel_calib2.png" width="50%">

4. 将机体置于各个方向并重复以上步骤。

## 查看&保存结果

1. 校准的结果可以通过在控制台输入`param list CALIB`指令查看。

```
msh />param list CALIB
CALIB:
......
       ACC0_XOFF: -0.265755
       ACC0_YOFF: 0.072022
       ACC0_ZOFF: -0.140535
    ACC0_XXSCALE: 0.999727
    ACC0_YYSCALE: 1.000000
    ACC0_ZZSCALE: 0.990056
    ACC0_XYSCALE: 0.000000
    ACC0_XZSCALE: 0.000000
    ACC0_YZSCALE: 0.000000
......
```

2. 控制台输入`param save`保存校准结果，否则系统断电将丢失未保存的校准结果。
