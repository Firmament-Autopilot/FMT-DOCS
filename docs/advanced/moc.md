使用动捕定位需要将导航的相关参数设置如下：

 <p align="left">
 	<img src="./figures/moc2.png" width="60%">
 </p>

通过外部（如机载计算机）将动捕数据发送给飞控，如下所示，动捕数据将作为external_pos消息发布，使用mcn echo external_pos可以查看收到的动捕数据信息（请确保飞控这边收到的动捕数据为FRD坐标系，其中F方向跟动捕的X轴方向对应）。

 <p align="left">
 	<img src="./figures/moc3.png" width="60%">
 </p>

动捕系统的设置如下所示：

 <p align="left">
 	<img src="./figures/moc1.png" width="60%">
 </p>
