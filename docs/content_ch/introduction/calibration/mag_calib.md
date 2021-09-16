
## 进行校准

罗盘校准通过QGroundControl进行。遵循QGroundControl的指导完成校准流程。

1. 点击**Compass**按钮。

<img src="figures/mag_calib1.png" width="50%">

2. 点击**OK**开始校准。
3. 将机体置于红框所示（未完成）状态并保持不动。一旦黄色框出现，将机体沿着指定轴进行旋转。当前方向校准完成后屏幕上对应图标将变为绿框。

<img src="figures/mag_calib2.png" width="50%">

4. 对各个方向重复校准步骤。

## 查看&保存结果

1. 校准的结果可以通过在控制台输入`param list CALIB`指令查看。

```
msh />param list CALIB
CALIB:
......
       MAG0_XOFF: -0.025247
       MAG0_YOFF: -0.067397
       MAG0_ZOFF: -0.136934
    MAG0_XXSCALE: 0.863891
    MAG0_YYSCALE: 0.912510
    MAG0_ZZSCALE: 0.931017
    MAG0_XYSCALE: 0.010489
    MAG0_XZSCALE: -0.022396
    MAG0_YZSCALE: 0.076960
......
```

2. 控制台输入`param save`保存校准结果，否则系统断电将丢失未保存的校准结果。
