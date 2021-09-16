
## 进行校准

水平校准通过QGroundControl进行。遵循QGroundControl的指导完成校准流程。

1. 点击**Level Horizon**按钮。

<img src="figures/level_calib1.png" width="50%">

2. 将机体按水平飞行方向放置在平面上。
3. 点击**OK**按钮开始校准。
4. 等待校准完成。

<img src="figures/level_calib2.png" width="50%">

## 查看&保存结果

1. 校准的结果可以通过在控制台输入`param list CALIB`指令查看。

```
msh />param list CALIB
CALIB:
......
      LEVEL_XOFF: -0.017828
      LEVEL_YOFF: -0.000741
      LEVEL_ZOFF: 0.000000
```

2. 控制台输入`param save`保存校准结果，否则系统断电将丢失未保存的校准结果。
