## 外部定位

FMT的惯性导航系统（INS）支持使用外部定位传感器进行数据融合，包括三维激光雷达，UWB，VINS，动捕等。

这些外部定位数据都通过飞控内部的`external_pos`的uMCN消息发布给导航系统进行融合。external_pos消息的定义如下所示：

| Type   | Name        | Unit | Comments                                                     |
| ------ | ----------- | ---- | ------------------------------------------------------------ |
| uint32 | timestamp   | ms   | external pos timestamp                                       |
| uint32 | field_valid | -    | field bit valid: 0: pos(xy) valid 1: height(z) valid 2: attitude(roll/pitch) valid 3: heading(yaw) valid |
| single | x           | m    | local position x                                             |
| single | y           | m    | local position y                                             |
| single | z           | m    | local position z                                             |
| single | phi         | rad  | roll angle                                                   |
| single | theta       | rad  | pitch angle                                                  |
| single | psi         | rad  | yaw angle                                                    |

**其中xyz为飞控NED坐标系的水平位置和高度数据**，即x轴正方向应该跟飞控的0°方向对应。若不对应的话，需设置`EXTPOS_PSI_OFS`参数（单位rad）来将外部定位的xyz旋转到飞控的NED方向。

`filed_valid`用来控制哪些外部定位的数据需要导航进行融合，其受如下参数控制：

- EXTPOS_USE_POS：为1表示融合外部定位的xy数据
- EXTPOS_USE_ALT：为1表示融合外部定位的z数据
- EXTPOS_USE_ATT：为1表示融合外部定位的phi和theta数据
- EXTPOS_USE_PSI：为1表示融合外部定位的psi数据

外部定位的航向角`psi`的范围如下所示，其角度沿顺时针范围增大，单位为rad。

 <p align="left">
 	<img src="./content_ch/advanced/figures/external_psi.png" width="30%">
 </p>
