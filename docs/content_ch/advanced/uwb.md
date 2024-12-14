## UWB

本章主要讲解如何使用UWB（Ultra Wide Band）设备进行定位。本章使用的Nooploop公司的Linktrack UWB定位模块。共需要使用到6个Linktrack模块，其中1个作为移动端，4个作为基站端，1个作为控制台。

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_usage.png" width="60%">
 </p>

#### 连接

移动端UWB与飞控的任一串口连接。以CUAV V5+为例，将UWB与飞控的UART4端口连接。

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_connect.png" width="60%">
 </p>

#### 上位机

将UWB Console模块通过USB连接至PC，打开NAssistant上位机。选择UWB Console模块对应端口，点击连接。

> NAssistant将自动查询UWB模块的波特率，故无需设置。

 <p align="left">
 	<img src="./content_ch/advanced/figures/nassistant.png" width="60%">
 </p>

#### 配置移动端（TAG）
将移动端安装至无人机之前我们首先需要对其进行设置。将其通过USB连接至计算机，并使用NAssistant连接。按下图对角色、ID、协议、波特率等进行配置，配置完成后点击写入参数即可。

 <p align="left">
 	<img src="./content_ch/advanced/figures/tag.png" width="60%">
 </p>

#### 配置基站端（ANCHOR）
基站用于所围区域内的移动端的定位，使用前也需要对其进行配置。将四个基站端分别连接至电脑并使用NAssistant连接。按下图对模块角色、协议、波特率进行配置，id依次设置为为0、1、2、3，并安装在支架上。依次对应基站A0、A1、A2、A3。

 <p align="left">
 	<img src="./content_ch/advanced/figures/anchor.png" width="60%">
 </p>

#### 配置控制台（CONSOLE）
控制台用于观测移动端以及基站的数据和状态，同时用于存储校准后的参数值。将控制台连接至电脑并使用NAssistant按如下进行配置。

 <p align="left">
 	<img src="./content_ch/advanced/figures/console.png" width="60%">
 </p>

#### 基站标定

将四个基站端按下图所示位置进行摆放，基站布局范围推荐大于3m*3m（小于也可以定位），基站范围长宽比4基站模式一般建议小于3:1，基站模块需保持在同一高度（高度误差不超过20cm），否则将影响定位精度。同时基站不要直接放在地面，离地面或其它支撑平面至少50cm以上，基站天线一般保持竖直向上即可。

 <p align="left">
 	<img src="./content_ch/advanced/figures/anchor_pos.png" width="40%">
 </p>

给所有基站和移动端上电，并且将控制台连接至NAssistant地面站。点击无线设置，查看网络内所有正常组网的模块。其中C0为控制台，A0、A1、A2、A3为基站端，T0为移动站。

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_status.png" width="60%">
 </p>

点击一键标定进行基站标定，标定过程可以看到右侧参数在不断变化，在2D界面能看到A0-A3四个基站以及T0的相对位置。

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_calib.png" width="60%">
 </p>

  <p align="left">
 	<img src="./content_ch/advanced/figures/2d_pos.jpg" width="60%">
 </p>

> 每次若移动基站位置，需重新进行标定。标定数据存储在Console中，故每次使用UWB需给Console上电才能正确进行定位。

#### 飞控配置
飞控端需要打开UWB的驱动，以使其使用UWB进行定位。打开对应BSP的`board.c`文件，在*bsp_initialize()*函数中添加`drv_nlink_linktrack_init("serial3");`该函数用于初始化UWB驱动，同时定义其使用的设备为`serial3`。

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_driver.png" width="60%">
 </p>

> 注意：需将其它使用了`serial3`的驱动初始化函数注释掉。

给UWB的所有模块和飞控进行上电，通过飞控控制台输入`mcn echo external_pos`指令，查看定位数据是否有效。如下`valid xy:1`表示UWB的水平位置有效，说明正常接收到UWB的数据。

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_data.png" width="50%">
 </p>

> 注意：默认飞控仅使用了UWB的水平位置数据，而未使用高度数据。

在确认正常接收到UWB的数据后，我们还需要对UWB数据在飞控端进行标定，以将UWB基站的位置坐标信息跟飞控的坐标进行同步，即让飞控知道UWB的基站坐标系和飞控坐标系的转换关系。

首先将导航中相关参数设置为如下所示的值，其中EXT_PSI_MODE设置为1，表示使用EXT_PSI的参数值作为UWB的朝向。

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_parameter.png" width="50%">
 </p>

将机头朝向UWB的X轴（A0->A3方向），然后在飞控控制台输入`calib extpos`指令进行校准。该指令将获取当前飞机的航向角度并存储在`EXTPOS_PSI`参数（单位rad）中。校准后记得输入`param save`指令来存储校准的参数。

 <p align="left">
 	<img src="./content_ch/advanced/figures/calib_extpos.png" width="50%">
 </p>

> 在校准UWB朝向之前请先确保飞控的磁罗盘已经校准，以获得正确的航向角。

> 若UWB的基站位置发生移动，需重新校准。