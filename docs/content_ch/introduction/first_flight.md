## 首次飞行

在首次飞行前，您需要进行一些检查以确保安全。

## 检查遥控器

首先，您需要检查遥控功能是否正常工作。首先通过输入 `mcn list` 来验证是否接收到遥控数据，检查 `rc_channels` 数据的发布频率是否非零。如果发布频率为零，这表示未接收到 RC 信号，请确保您的 RC 接收器正确连接并且您的 `sysconfig.toml` 配置正确。有关 RC 配置的更多信息，请参阅[遥控校准](http://localhost:3000/#/content_ch/introduction/calibration/rc_calib)。

```
msh />mcn list
Topic                 #SUB   Freq(Hz)   Echo   Suspend
-------------------- ------ ---------- ------ ---------
sensor_imu0_0           0     1000.0    true    false
sensor_imu0             1     1000.0    true    false
sensor_mag0_0           0      100.0    true    false
sensor_mag0             1      100.0    true    false
sensor_baro             1      100.0    true    false
sensor_optflow          1       0.0     true    false
sensor_rangefinder      1       0.0     true    false
external_state          0       0.0     false   false
ins_output              3      500.0    true    false
external_pos            1       0.0     true    false
fms_output              4      250.0    true    false
control_output          2      500.0    true    false
pilot_cmd               3      19.8     true    false
rc_channels             0      19.8     true    false
rc_trim_channels        1      19.8     true    false
gcs_cmd                 2       1.0     true    false
auto_cmd                1       0.0     true    false
mission_data            2       0.0     true    false
bat_status              0       2.0     true    false
```

接下来，检查摇杆映射是否正确。映射后的遥控数据通过 `pilot_cmd` 消息发布。输入 `mcn echo pilot_cmd` 查看映射后的摇杆数据。分别将油门、横滚、俯仰和偏航摇杆移动到它们的极限位置和中间位置，并验证输出数据是否正确。

当摇杆完全向下或向左推时，输出应为 -1。当摇杆完全向上或向右推时，输出应为 1。当摇杆位于中间位置时，输出应为 0。

```
msh />mcn echo pilot_cmd
stick_yaw:0.03 stick_throttle:-0.80 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.70 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.02 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:0.87 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:0.66 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:-0.69 stick_throttle:-0.05 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:-0.81 stick_throttle:-0.02 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:0.03 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.87 stick_throttle:-0.18 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.03 stick_pitch:0.02 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.01 stick_pitch:-0.77 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.01 stick_pitch:0.89 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.01 stick_pitch:0.24 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:-0.83 stick_pitch:-0.01 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.04 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.85 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.03 stick_pitch:0.05 mode:5 cmd:[0 0]
stick_yaw:0.03 stick_throttle:-0.09 stick_roll:0.01 stick_pitch:0.05 mode:5 cmd:[0 0]
```

>如果误差过大，请重新校准遥控器。

接下来，检查模式切换是否正常工作。根据默认的 toml 设置，SWC 开关（RC 通道 5）对应以下模式：

- Up: Position mode
- Middle: Altitude mode
- Down: Stabilize mode


通过检查控制台，验证飞控的控制台输出模式是否与 sysconfig.toml 中的模式设置匹配，应分别为 Position（位置）、Altitude（高度）或 Stabilize（稳定）。

```
[1524961] I/StatusTask: [Pilot Mode] Stabilize
[1528078] I/GCS: set Altitude mode.
[1528085] I/StatusTask: [Pilot Mode] Altitude
[1528582] I/GCS: set Position mode.
[1528589] I/StatusTask: [Pilot Mode] Position
```
最后，检查紧急锁定开关是否正常工作。紧急锁定开关用于在紧急情况下一键锁定飞机，例如当飞机无法自动锁定或碰撞到障碍物时。

将遥控器上的 SWD（RC channel 6）开关从上位移动到下位以触发 Disarm 命令，并通过控制台进行验证。

```
[1519363] I/StatusTask: [FMS Cmd] Disarm
```

>这个命令是边缘触发的，因此将 SWD 开关从上位移动到下位只会触发一次 Disarm 命令。要再次触发它，您需要将 SWD 开关先移回上位，然后再次移动到下位。

<p align="center">
  <img src="figures/rc_figure.png" width="40%">
</p>

## 检查解锁/上锁

对于飞行器的解锁操作，请将左摇杆移动到右下角并保持在那里，如下图所示。大约1.5秒后，飞行器将解锁，并且电机将开始空转。逐渐增加油门，当油门超过一个阈值（通常是中间位置）时，它将从待机状态过渡到解锁状态。

在待机模式下，将左摇杆移动到左下角并保持在那里；大约1.5秒后，飞行器将被锁定。

<p align="center">
  <img src="figures/arm_disarm.png" width="40%">
</p>

解锁飞行器后，减小油门。当飞行器接触地面时，将油门拉到最小位置，飞行器将自动锁定。

飞行器的解锁和上锁操作也可以通过 QGroundControl (QGC) 地面站进行。

<p align="center">
  <img src="figures/qgc_arm.png" width="60%">
</p>

## 检查电机

首先，检查电机的安装顺序和旋转方向非常重要，因为错误安装电机可能会导致飞行器受损。要验证电机的安装顺序和方向，请参考[载具/机架](http://localhost:3000/#/content_ch/introduction/frameref)部分。

要验证电机的安装情况，您可以使用 act 命令逐个驱动每个电机。不过，在执行此操作之前，请出于安全考虑移除所有电机螺旋桨。

首先禁用控制器的输出，以防止其覆盖 act 指令的输出。

```
mcn suspend control_output
```

要选择电机通道，您可以使用 `-c` 或 `--channel` 选项，后面跟着一个表示通道位掩码的十六进制值。例如，使用以下命令可以驱动或停止电机1。将 "1" 替换为 "2" 可以控制电机2，"4" 控制电机3，"8" 控制电机4，依此类推。

```
act set -d main_out -c 1 1200
act set -d main_out -c 1 1000
```

经测试过后，若电机运转正常，你可以恢复控制器的输出。
```
mcn resume control_output
```

为了简化这个过程，您可以将所有这些命令整合到一个名为 `check_motor.sh` 的脚本中，如下所示：

```
mcn suspend control_output
act set -d main_out -c 1 1200
delay 3000
act set -d main_out -c 1 1000
delay 1000
act set -d main_out -c 2 1200
delay 3000
act set -d main_out -c 2 1000
delay 1000
act set -d main_out -c 4 1200
delay 3000
act set -d main_out -c 4 1000
delay 1000
act set -d main_out -c 8 1200
delay 3000
act set -d main_out -c 8 1000
delay 1000
mcn resume control_output
```
然后通过执行以下命令来运行这个脚本
```
exec check_motor.sh
```

## 检查姿态
在飞行之前，您还应该通过 QGroundControl (QGC) 验证姿态是否正确。这样可以确保您的飞行控制器安装在正确的位置和方向，并且传感器正确校准。

## 检查飞行模式
在起飞前，请确保地面站显示的飞行模式与您设置的飞行模式匹配。
<p align="center">
  <img src="figures/check_mode.png" width="60%">
</p>
如果它们不匹配，可能是因为传感器数据未达到要求，导致控制能力下降。例如，如果您已将飞行模式设置为位置模式（Position），但地面站显示为高度模式（Altitude），可能的原因是缺乏 GPS 连接或 GPS 精度不足，导致位置信息不足，从而无法进入位置模式。

您还可以通过输入 `mcn echo ins_output` 来检查惯性导航系统（INS）的状态。如果 `xy` 和 `h` 标志都为零，表示位置和高度信息不可用，这意味着飞行器无法进入位置模式。
```
msh />mcn echo ins_output
timestamp:206396
att: -0.01 -0.03 -0.01
rate: 0.01 0.00 -0.00
accel: -0.10 -0.01 -9.86
vel: 0.00 0.00 -0.00 airspeed:0.10
xyh: -0.06 -0.08 0.05, h_AGL: 0.05
LLA: 0.656730 -2.136100 4.557099 LLA0: 0.656730 -2.136100 4.509699
dx/dlat: 6359226.852620 dy/dlon: 5057753.479151
standstill:1 att:1 heading:1 vel:1 LLA:1 xy:1 h:1 h_AGL:0
sensor status, imu1:1 imu2:0 mag:1 baro:1 gps:1 rf:0 optflow:0
```

## 模拟飞行
对于不熟悉飞行控制的用户，建议先使用 SIH 模拟模式进行飞行练习，以熟悉飞行器的操作。SIH 的操作与真实飞行器完全一致。有关如何启用 SIH 模拟模式的说明，请参考 [Simulation-In-Hardware](http://localhost:3000/#/content_ch/simulation/SIH) 中的模拟步骤。

## 实物飞行
出于安全考虑，建议在宽敞的户外区域进行测试。对于初学者，建议从位置模式开始飞行。
