## 激光雷达

本章主要讲解如何使用三维激光雷达进行融合定位。


#### 发送定位数据

三维激光雷达可以获取实时XYZ位置和高度信息，以及航向角信息。将激光雷达的外部定位数据（包含XYZ以及航向）通过`MAVLINK_MSG_ID_VISION_POSITION_ESTIMATE`的mavlink消息经由`mavobc`的链路（机载计算机链路）发送给飞控。飞控将收到该条消息后，将其解析为`external_pos`的飞控内部uMCN消息进行发布，再由导航进行接收和融合。

#### 飞控配置

将导航的外部定位的融合参数设置为如下所示：

```
                EXTPOS_POS_GAIN: 8.000000
                EXTPOS_VEL_GAIN: 6.000000
                EXTPOS_BIAS_A_GAIN: 0.200000
                EXTPOS_POS_DELAY: 30
                EXTPOS_PSI_OFS: 0.000000
```

设置参数融合激光雷达的水平位置、高度和航向角：

                    EXTPOS_USE_POS: 1
                    EXTPOS_USE_ALT: 1
                    EXTPOS_USE_ATT: 0
                    EXTPOS_USE_PSI: 1

完成后，在飞控控制台输入`mcn echo external_pos`指令，查看定位数据是否有效。
