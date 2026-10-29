## 在线调参
在线参数调节是指直接通过地面站或者飞控终端修改参数值以及查看控制曲线，而无需重新烧写固件或者下载日志的方式来调节参数。这种方法操作相对简便，适合快速参数调节。

本章节将以多旋翼为例介绍在线参数调节的过程，其它载具可类似操作。

### 读取参数
要访问控制参数，只需在控制台中输入 `param list CONTROL`。该命令将显示所有控制参数及其当前值的列表。

```
msh />param list CONTROL
CONTROL:
   [r]                 AIRFRAME: 1
                    MC_VEL_XY_P: 1.400000
                    MC_VEL_XY_I: 0.200000
                    MC_VEL_XY_D: 0.050000
                     MC_VEL_Z_P: 0.500000
                     MC_VEL_Z_I: 0.120000
                     MC_VEL_Z_D: 0.000000
                MC_VEL_XY_I_MIN: -1.000000
                MC_VEL_XY_I_MAX: 1.000000
                MC_VEL_XY_D_MIN: -1.000000
                MC_VEL_XY_D_MAX: 1.000000
                 MC_VEL_Z_I_MIN: -0.200000
                 MC_VEL_Z_I_MAX: 0.200000
                 MC_VEL_Z_D_MIN: -0.100000
                 MC_VEL_Z_D_MAX: 0.100000
                      MC_ROLL_P: 7.000000
                     MC_PITCH_P: 7.000000
              MC_ROLL_PITCH_LIM: 0.523599
                  MC_ROLLRATE_P: 0.150000
                 MC_PITCHRATE_P: 0.150000
                   MC_YAWRATE_P: 0.300000
                  MC_ROLLRATE_I: 0.150000
                 MC_PITCHRATE_I: 0.150000
                   MC_YAWRATE_I: 0.300000
                  MC_ROLLRATE_D: 0.005000
                 MC_PITCHRATE_D: 0.005000
                   MC_YAWRATE_D: 0.002000
                 MC_ROLLRATE_FF: 0.030000
                MC_PITCHRATE_FF: 0.030000
                  MC_YAWRATE_FF: 0.050000
                  MC_RATE_I_MIN: -0.200000
                  MC_RATE_I_MAX: 0.200000
                  MC_RATE_D_MIN: -0.200000
                  MC_RATE_D_MAX: 0.200000
                 MC_RATE_XY_LIM: 1.570796
                  MC_RATE_Z_LIM: 3.141593
                  MC_HOVER_THRO: 0.500000

```
> 如果您将某个参数从其默认值更改了，列表中该参数的开头会显示一个 `*` 符号。要显示已更改的参数，可以使用 `-c` 选项；要显示默认值，可以使用 `-d` 选项。

### 修改参数
一般来说，对于一台新飞机，需要调节的参数仅仅是最内环的控制参数（角速度控制环），当内环调好了，外环如姿态、速度、位置环自然就稳定了。

角速度环参数位于控制器中，关于参数定义可以查看模型的[README](https://github.com/Firmament-Autopilot/FMT-Model/blob/master/model/Controller/MC-Controller/README.md)，如果想要更进一步，可以查看模型源码。

在飞控终端输入`param list CONTROL`可以列出当前控制器模型的所有参数，一般来说，我们只需要调节角速度环的参数，如下所示：

```
                  MC_ROLLRATE_P: 0.150000
                 MC_PITCHRATE_P: 0.150000
                   MC_YAWRATE_P: 0.300000
                  MC_ROLLRATE_I: 0.150000
                 MC_PITCHRATE_I: 0.150000
                   MC_YAWRATE_I: 0.300000
                  MC_ROLLRATE_D: 0.005000
                 MC_PITCHRATE_D: 0.005000
                   MC_YAWRATE_D: 0.002000
                 MC_ROLLRATE_FF: 0.030000
                MC_PITCHRATE_FF: 0.030000
                  MC_YAWRATE_FF: 0.050000
                  MC_RATE_I_MIN: -0.200000
                  MC_RATE_I_MAX: 0.200000
                  MC_RATE_D_MIN: -0.200000
                  MC_RATE_D_MAX: 0.200000
                 MC_RATE_XY_LIM: 1.570796
                  MC_RATE_Z_LIM: 3.141593
                  MC_HOVER_THRO: 0.500000
```

预设的控制参数可能不适合您的具体飞行器，因此需要根据您的需求手动微调这些参数。

> 对于多旋翼而言，默认参数针对的是410轴距左右的旋翼，一般来说，如果您的机架大于该轴距，需要将角速率的PID参数等比例增大，反之，则等比例减小。

推荐的方法是使用 `param` 命令修改参数。例如，要将 ROLL 的角速度参数 P 设置为 0.1，可以使用以下命令：

```
param set MC_ROLLRATE_P 0.1
```
> 我们可以通过`param set <parameter> <value>`指令来设定参数值，其中*parameter*是待修改的参数名称，*value*的新的参数值。注意，如果待修改的值为负数，则需要在其前面加上双斜杠`--`，例如`param set RATE_I_MIN -- -0.1`。关于更多param set的用法，可以输入param set --help。

除了指令以外，我们也可以通过地面站来修改参数，我们可以在地面站的参数界面的搜索框中输入我们要修改的参数名，然后点击该参数即可输入新的数值。

<p align="center">
  <img src="content_cn/introduction/figures/qgc_parameter.png" width="40%">
</p>
修改参数后，无需重新启动飞控器，修改会立即生效。因此理论上，您可以在飞行过程中进行参数微调。然而，出于安全考虑，建议在起飞前在地面上调整参数。
调整一组控制效果稳定的参数后，请记得输入 `param save` 保存它们，以防止在断电时丢失参数。



### 查看控制曲线

为了观察控制器的控制效果，我们通常需要将控制器的目标信号和实际信号绘制出来。

首先在控制器的Simulink模型中找到要查看的角速度环的目标信号和实际信号，并对其命名。然后右键单击信号线选择*Properties*，将*Storage class*修改为`ExportedGlobal`将其进行导出，以便我们在飞控嵌入式中进行读取。

<p align="center">
 <img src="content_cn/introduction/figures/signal_export.png" width="40%">
</p>

重新编译生成Controller的代码，在*Controller.h*中我们将可以看到我们导出的目标信号。

<p align="center">
 <img src="content_cn/introduction/figures/signal_export_gen.png" width="40%">
</p>

然后我们在`control_interface.c`文件中添加如下代码，将信号以50ms周期发送到地面站。

```c
#include "module/mavproxy/mavproxy.h"
...
void control_interface_step(uint32_t timestamp)
{
	....
    DEFINE_TIMETAG(debug_tt, 50);
    if (check_timetag(TIMETAG(debug_tt))) {
        mavlink_system_t mavsys = mavproxy_get_system();
        uint64_t time_usec = systime_now_us();
        mavlink_message_t msg;

        mavlink_msg_debug_vect_pack(mavsys.sysid, mavsys.compid, &msg, "rate_cmd", time_usec, rate_cmd_B_radPs[0], rate_cmd_B_radPs[1], rate_cmd_B_radPs[2]);
        mavproxy_send_immediate_msg(MAVPROXY_GCS_CHAN, &msg, false);

        mavlink_msg_debug_vect_pack(mavsys.sysid, mavsys.compid, &msg, "rate_est", time_usec, rate_est_B_radPs[0], rate_est_B_radPs[1], rate_est_B_radPs[2]);
        mavproxy_send_immediate_msg(MAVPROXY_GCS_CHAN, &msg, false);
    }    
}
```

打开地面站的`Mavlink Analyze`界面，我们可以看到发送过来的信号信息。通过观察目标信号（rate_cmd）和实际信号（rate_est）就可以知道控制的误差，根据误差来实时调节控制参数。

<p align="center">
 <img src="./content_cn/introduction/figures/qgc_analyze.png" width="40%">
</p>
