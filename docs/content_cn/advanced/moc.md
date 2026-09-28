## 动捕定位

本章主要讲解如何使用动捕系统（Motion Capture System，MOCAP）设备进行定位。

#### 发送定位数据

将动捕的定位数据（包含XYZ以及航向）通过`MAVLINK_MSG_ID_VISION_POSITION_ESTIMATE`的mavlink消息经由`mavobc`（机载计算机链路）的链路发送给飞控。飞控将接受该条消息，并将解析的数据封装为`external_pos`的飞控内部uMCN消息发布，再由导航进行接收和融合。

#### 飞控配置

使用动捕定位首先需要将飞控导航的相关参数设置如下。其中`EXTPOS_PSI_OFS`参数作为动捕系统X轴方向的航向角偏移，这里设置为pi/2，表示当飞机机头朝向动捕的X轴时，飞机的航向角（由动捕系统给出）为90度。这一步是为了将飞机的坐标系与动捕系统的坐标系进行对齐。

```
				EXTPOS_POS_GAIN: 8.000000
                EXTPOS_VEL_GAIN: 6.000000
                EXTPOS_BIAS_A_GAIN: 0.200000
                EXTPOS_POS_DELAY: 30
                EXTPOS_PSI_OFS: 1.570800
```

设置参数融合VINS的水平位置、高度和航向角：

```
                EXTPOS_USE_POS: 1
                EXTPOS_USE_ALT: 1
                EXTPOS_USE_ATT: 0
                EXTPOS_USE_PSI: 1
```



完成后，在飞控控制台输入`mcn echo external_pos`指令，查看定位数据是否有效。

 <p align="left">
 	<img src="./content_ch/advanced/figures/moc3.png" width="40%">
 </p>



动捕系统的设置如下所示，方向为Z_UP，这样确保经过mavros转换（如果用到的话）后的飞控收到的外部定位数据的坐标系为FRD坐标系，其中F方向跟动捕的X轴方向对应。

 <p align="left">
 	<img src="./content_ch/advanced/figures/moc1.png" width="60%">
 </p>

