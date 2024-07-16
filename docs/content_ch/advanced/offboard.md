## 板外控制

> 板外控制存在一定的危险性。在进行板外飞行之前，开发者有责任确保充分的准备、测试和安全预防措施。

板外模式用于通过设置位置、速度、加速度、姿态、姿态速率或推力/扭矩设定点来控制飞行器的运动和姿态。

板外控制的核心思想是通过在自动驾驶仪之外运行的软件来控制 FMT 飞行栈。这是通过 MAVLink 协议实现的，具体是通过 [SET_POSITION_TARGET_LOCAL_NED](https://mavlink.io/en/messages/common.html#SET_POSITION_TARGET_LOCAL_NED) 和 [SET_ATTITUDE_TARGET](https://mavlink.io/en/messages/common.html#SET_ATTITUDE_TARGET) 消息来完成的。

板外控制的标准流程如下：

1. 激活板外控制模式。
2. 发送起飞命令，启动升空至指定高度。
3. 一旦飞行器起飞，由于尚未发送板外命令，飞行器将过渡到保持模式。
4. 在机载计算机内部启动板外任务，向飞行控制器发送命令。

> 您可以利用 [SIH](simulation/SIH.md) 模拟模式，在进行真实测试之前，先测试板外控制的程序。

### MAVROS 控制

目前，FMT 在 ROS Melodic 上为大多数 MAVROS 消息功能提供支持，并已成功进行测试。以下部分介绍了如何使用 FMT 的 MAVROS 功能。所有示例均基于 ICF5 平台，类似的配置也适用于其他硬件平台。

#### 通信接口配置

首先，您需要在飞行控制器上配置 `sysconfig.toml` 文件，以设置飞行控制器与机载计算机之间的通信接口。您需要按照以下示例配置 `mavproxy`。在这个配置中，定义了三个设备：两个设备在通道 0 (`chan=0`) 上用于与地面站通信，一个设备在通道 1 (`chan=1`) 上用于与机载计算机通信。因此，此配置使用 `serial2`，对应于飞行控制器上的 TELEM2 端口，用于与机载计算机通信。

```toml
# Mavproxy Device Configuration
[mavproxy]
    # ground control station channel devices
    [[mavproxy.devices]]
    chan = 0
    type = "serial"
    name = "serial1"
    baudrate = 57600

    [[mavproxy.devices]]
    chan = 0
    type = "usb"
    name = "usbd0"
    auto-switch = true

    # onboard computer channel devices
    [[mavproxy.devices]]
    chan = 1
    type = "serial"
    name = "serial2"
    baudrate = 115200
```

如果您希望使用 USB 与机载计算机通信，可以按照以下配置进行设置。

```toml
# Mavproxy Device Configuration
[mavproxy]
    # ground control station channel devices
    [[mavproxy.devices]]
    chan = 0
    type = "serial"
    name = "serial1"
    baudrate = 57600

	# onboard computer channel devices
    [[mavproxy.devices]]
    chan = 1
    type = "usb"
    name = "usbd0"
```

> 在修改 `sysconfig.toml` 文件后，您需要将其上传到飞行控制器的机载文件系统中的 `/sys` 目录下，然后重新启动飞行控制器，使更改生效。

#### 启动 MAVROS

首先，在飞行控制器的串口或 USB 端口与机载计算机之间建立连接。假设机载计算机运行 Ubuntu，相应的设备应该会出现在 Ubuntu 的 `/dev` 目录中，例如 `/dev/ttyACM0`。


 <p align="center">
 	<img src="./figures/ttyacm0.png" width="40%">
 </p>

修改 MAVROS 中的 `px4.launch` 文件，使设备端口和波特率与设备配置一致。

 <p align="center">
 	<img src="./figures/px4_launch.png" width="40%">
 </p>

然后，使用以下命令来运行 MAVROS：

```
roslaunch mavros px4.launch
```

成功启动 MAVROS 应该会看到 `Got HEARTBEAT` 消息。


 <p align="center">
 	<img src="./figures/mavros.png" width="40%">
 </p>

 #### 从机载计算机控制飞行器

目前，FMT 已经支持机载计算机的以下 MAVLink 消息。如果您需要支持其他消息，请通知我们进行评估，或者您可以修改 `mavobc.c` 中的代码，添加对应的 MAVLink 消息处理。

- 模式设置
- 解锁/上锁
- 起飞/降落/返航/保持/暂停/继续命令支持
- MAVLINK_MSG_ID_SET_ATTITUDE_TARGET（支持坐标系：FRAME_BODY_FRD）
- MAVLINK_MSG_ID_SET_POSITION_TARGET_LOCAL_NED（支持坐标系：MAV_FRAME_LOCAL_NED、MAV_FRAME_LOCAL_FRD、MAV_FRAME_BODY_FRD）
- MAVLINK_MSG_ID_SET_POSITION_TARGET_GLOBAL_INT（支持坐标系：MAV_FRAME_GLOBAL_INT）

以下是一些测试 ROS 命令。您也可以使用 C++ 或 Python 调用 ROS 接口，结果将是相同的。

```
# Set Flight Mode

# Position Mode
rosservice call /mavros/set_mode "base_mode: 0
custom_mode: 'POSCTL'"

# Mission Mode
rosservice call /mavros/set_mode "base_mode: 0
custom_mode: 'AUTO.MISSION'"

# Offboard Mode
rosservice call /mavros/set_mode "base_mode: 0
custom_mode: 'OFFBOARD'"
```

> 请注意，仅当遥控器处于关闭状态时，才可以通过机载计算机设置模式。这是因为遥控器的模式优先于地面站和机载计算机。

```
# Send Command

# Disarm/Arm
rosservice call /mavros/cmd/arming "value: true"
rosservice call /mavros/cmd/arming "value: false"

# Takeoff
rosservice call /mavros/set_mode "base_mode: 0
custom_mode: 'AUTO.TAKEOFF'"

# Land
rosservice call /mavros/set_mode "base_mode: 0
custom_mode: 'AUTO.LAND'"

# Return
rosservice call /mavros/set_mode "base_mode: 0
custom_mode: 'AUTO.RTL'"

# Hold
rosservice call /mavros/set_mode "base_mode: 0
custom_mode: 'AUTO.LOITER'"
```

只有当飞行器处于板外模式时，发送目标位置命令才有效。如果进入板外模式，但未向飞行控制器发送控制信号命令（即 `auto_cmd` 消息的发布频率为 0），飞行器将进入保持模式，悬停在原地。

```
rostopic pub /mavros/setpoint_raw/local mavros_msgs/PositionTarget "header:
  seq: 0
  stamp:
    secs: 0
    nsecs: 0
  frame_id: ''
coordinate_frame: 1
type_mask: 2560
position:
  x: 100.0
  y: 50.0
  z: 10.0
velocity:
  x: 0.0
  y: 0.0
  z: 0.0
acceleration_or_force:
  x: 0.0
  y: 0.0
  z: 0.0
yaw: 0
yaw_rate: 0.0" -r 10
```

> 请注意，ROS 使用的坐标系（ENU）与飞行控制器使用的坐标系（NED）不同。Mavros 将执行坐标转换来解决这种差异。

发送这条消息后，飞行控制器将接收到 `auto_cmd` 消息。然后您可以在飞行控制器控制台上输入 `mcn echo auto_cmd` 命令来打印输出。

```
msh />mcn list
Topic               #SUB   Freq(Hz)   Echo   Suspend
------------------ ------ ---------- ------ ---------
sensor_imu0_0         0       0.0     true    false
sensor_imu0           1       0.0     true    false
sensor_mag0_0         0       0.0     true    false
sensor_mag0           1       0.0     true    false
sensor_baro           1       0.0     true    false
sensor_gps            1       0.0     true    false
sensor_airspeed       1       0.0     true    false
mav_ext_state         0       0.0     false   false
ins_output            3      500.0    true    false
fms_output            4      250.0    true    false
control_output        2      500.0    true    false
pilot_cmd             3       0.0     true    false
rc_channels           0       0.0     true    false
rc_trim_channels      1       0.0     true    false
gcs_cmd               2       1.0     true    false
auto_cmd              1       7.6     true    false
mission_data          2       0.0     true    false
bat_status            0       2.0     true    false
msh />mcn echo auto_cmd
timestamp:1973057 frame:0
psi: 1.57
x: 50.00
y: 100.00
z: -10.00
u: 0.00
v: 0.00
w: -0.00
ax: 0.00
ay: 0.00
az: -0.00
------------------------------------------
timestamp:1973551 frame:0
psi: 1.57
x: 50.00
y: 100.00
z: -10.00
u: 0.00
v: 0.00
w: -0.00
ax: 0.00
ay: 0.00
az: -0.00
------------------------------------------
```

同样地，您可以使用以下命令来发送目标姿态命令。

```
rostopic pub /mavros/setpoint_raw/attitude mavros_msgs/AttitudeTarget "header:
  seq: 0
  stamp: {secs: 0, nsecs: 0}
  frame_id: ''
type_mask: 7
orientation:
  x: 0.047
  y: 0.113
  z: 0.373
  w: 0.920
body_rate:
  x: 0.0
  y: 0.0
  z: 0.0
thrust: 0.6
" -r 10
```

要在飞行控制器上打印接收到的消息，请在飞行控制器的控制台上输入 `mcn echo auto_cmd`。

```
msh />mcn echo auto_cmd
timestamp:2051378 frame:2
phi: 0.17
theta: -0.17
psi: 0.79
throttle: 1600
------------------------------------------
timestamp:2051877 frame:2
phi: 0.17
theta: -0.17
psi: 0.79
throttle: 1600
```


#### 配置发送给机载计算机的消息

默认情况下，FMT 只会向机载计算机发送Heartbeat 消息（1Hz）。在从飞行控制器接收到Heartbeat 消息后，机载计算机需要向飞行控制器发送 MAVLINK_MSG_ID_REQUEST_DATA_STREAM 消息来配置它希望接收的消息。

FMT 支持以下机载计算机消息。如果您需要支持其他消息，请通知我们进行评估，或者您可以修改 `mavobc.c` 中的代码，添加对应的 MAVLink 消息发送功能。

 <p align="center">
 	<img src="./figures/mavobc.png" width="40%">
 </p>

您可以使用以下 ROS 示例代码来配置飞行控制器发送的消息及其传输速率。

```c++
#include <ros/ros.h>
#include <mavros_msgs/StreamRate.h>

int main(int argc, char **argv) {
    ros::init(argc, argv, "set_stream_rate_example");
    ros::NodeHandle nh;

    // Create a client for the /mavros/set_stream_rate service.
    ros::ServiceClient stream_rate_client = nh.serviceClient<mavros_msgs::StreamRate>("/mavros/set_stream_rate");

    // The message ID and frequency that are needed to be sent.
    int message_id = 105; // MAVLINK_MSG_ID_HIGHRES_IMU
    int message_rate = 100; // 将频率更改为 100Hz

    // Create a service request object.
    mavros_msgs::StreamRate srv;
    srv.request.stream_id = message_id;
    srv.request.message_rate = message_rate;
    srv.request.on_off = true; // Enable message send

    // Call service
    if (stream_rate_client.call(srv)) {
        ROS_INFO("Stream rate set successfully!");
    } else {
        ROS_ERROR("Failed to set stream rate.");
    }

    return 0;
}
```

### 机载控制

板外控制命令也可以通过机载任务发送。以下是一个示例，演示如何发送八字控制命令，控制无人机跟随八字路径。

```c
/******************************************************************************
 * Copyright 2023 The Firmament Authors. All Rights Reserved.
 *
 * Licensed under the Apache License, Version 2.0 (the "License");
 * you may not use this file except in compliance with the License.
 * You may obtain a copy of the License at
 *
 * http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing, software
 * distributed under the License is distributed on an "AS IS" BASIS,
 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 * See the License for the specific language governing permissions and
 * limitations under the License.
 *****************************************************************************/
#include <firmament.h>

#include "FMS.h"
#include "module/sysio/auto_cmd.h"
#include "module/task_manager/task_manager.h"

MCN_DECLARE(auto_cmd);

#define FLIGHT_ALTITUDE -1.5f
#define RATE            50   // loop rate hz
#define RADIUS          5.0 // radius of figure 8 in meters
#define CYCLE_S         30   // time to complete one figure 8 cycle in seconds
#define STEPS           (CYCLE_S * RATE)

static fmt_err_t task_init(void)
{
    return FMT_EOK;
}

static void task_entry(void* parameter)
{
    /* This demo reders to: https://gitlab.com/voxl-public/voxl-sdk/services/voxl-vision-px4/-/blob/f8223eff0e2a935ce05aa395e2d44739b7b322b4/src/offboard_figure_eight.c */
    printf("Offboard figure eight demo!\n");

    const float dt = 1.0f / RATE;
    const float dadt = (2.0f * PI) / CYCLE_S; // first derivative of angle with respect to time
    const float r = RADIUS;
    uint32_t i = 0;

    while (1) {
        // calculate the parameter a which is an angle sweeping from -pi/2 to 3pi/2
        // through the curve
        float a = (-PI / 2.0f) + i * (2.0f * PI / STEPS);
        float c = cos(a);
        float c2a = cos(2.0 * a);
        float c4a = cos(4.0 * a);
        float c2am3 = c2a - 3.0;
        float c2am3_cubed = c2am3 * c2am3 * c2am3;
        float s = sin(a);
        float cc = c * c;
        float ss = s * s;
        float sspo = (s * s) + 1.0; // sin squared plus one
        float ssmo = (s * s) - 1.0; // sin squared minus one
        float sspos = sspo * sspo;

        Auto_Cmd_Bus auto_cmd;

        auto_cmd.timestamp = systime_now_ms(),
        auto_cmd.frame = FRAME_GLOBAL_NED,
        auto_cmd.x_cmd = -(r * c * s) / sspo,
        auto_cmd.y_cmd = (r * c) / sspo,
        auto_cmd.z_cmd = FLIGHT_ALTITUDE,
        auto_cmd.u_cmd = dadt * r * (ss * ss + ss + (ssmo * cc)) / sspos,
        auto_cmd.v_cmd = -dadt * r * s * (ss + 2.0 * cc + 1.0) / sspos,
        auto_cmd.ax_cmd = -dadt * dadt * 8.0 * r * s * c * ((3.0 * c2a) + 7.0) / c2am3_cubed,
        auto_cmd.ay_cmd = dadt * dadt * r * c * ((44.0 * c2a) + c4a - 21.0) / c2am3_cubed,
        auto_cmd.psi_cmd = atan2(auto_cmd.v_cmd, auto_cmd.u_cmd),
        auto_cmd.cmd_mask = X_CMD_VALID
            | Y_CMD_VALID
            | Z_CMD_VALID
            | U_CMD_VALID
            | V_CMD_VALID
            | AX_CMD_VALID
            | AY_CMD_VALID
            | PSI_CMD_VALID;

        mcn_publish(MCN_HUB(auto_cmd), &auto_cmd);

        i = (i + 1) % STEPS;

        sys_msleep(dt * 1000);
    }
}

TASK_EXPORT __fmt_task_desc = {
    .name = "offboard",
    .init = task_init,
    .entry = task_entry,
    .priority = 25,
    .auto_start = false,
    .stack_size = 1024,
    .param = NULL,
    .dependency = NULL
};
```

 <p align="center">
 	<img src="./figures/eight_figure.jpg" width="40%">
 </p>

### ROS Offboard Example

We also provide a ROS example for 8-figure path control. The following is the example code. For the full ROS package, you can access https://github.com/Firmament-Autopilot/Demo/tree/main/ROS/offboard.

```c++
#include <ros/ros.h>
#include <mavros_msgs/SetMode.h>
#include <mavros_msgs/PositionTarget.h>

#define FLIGHT_ALTITUDE 1.0f
#define RATE            20   // loop rate hz
#define RADIUS          5.0 // radius of figure 8 in meters
#define CYCLE_S         30   // time to complete one figure 8 cycle in seconds
#define STEPS           (CYCLE_S * RATE)

int main(int argc, char** argv)
{
    ros::init(argc, argv, "offboard_figure_eight_demo");
    ros::NodeHandle nh;
    
    ROS_INFO("Offboard figure eight demo!");

    const float PI = 3.14159265359;
    const float dt = 1.0f / RATE;
    const float dadt = (2.0f * PI) / CYCLE_S;
    const float r = RADIUS;
    uint32_t i = 0;

    ros::Rate rate(RATE);

    ros::ServiceClient set_mode_client = nh.serviceClient<mavros_msgs::SetMode>("mavros/set_mode");

    mavros_msgs::SetMode set_mode;

    set_mode.request.custom_mode = "OFFBOARD";
    // Set the mode to OFFBOARD using MAVROS service
    if (set_mode_client.call(set_mode) && set_mode.response.mode_sent) {
        ROS_INFO("Offboard mode enabled");
    }

    set_mode.request.custom_mode = "AUTO.TAKEOFF";
    // Set the mode to TAKEOFF using MAVROS service
    if (set_mode_client.call(set_mode) && set_mode.response.mode_sent) {
        ROS_INFO("Takeoff enabled");
    }

    // delay 10s to wait takeoff finish
    ros::Duration(10).sleep();

    mavros_msgs::PositionTarget setpoint_msg;
    ros::Publisher setpoint_pub = nh.advertise<mavros_msgs::PositionTarget>("/mavros/setpoint_raw/local", 10);

    while (ros::ok()) {
        // Your code here
        float a = (-PI / 2.0f) + i * (2.0f * PI / STEPS);
        float c = cos(a);
        float c2a = cos(2.0 * a);
        float c4a = cos(4.0 * a);
        float c2am3 = c2a - 3.0;
        float c2am3_cubed = c2am3 * c2am3 * c2am3;
        float s = sin(a);
        float cc = c * c;
        float ss = s * s;
        float sspo = (s * s) + 1.0;
        float ssmo = (s * s) - 1.0;
        float sspos = sspo * sspo;

        i = (i + 1) % STEPS;

        setpoint_msg.header.stamp = ros::Time::now();
        setpoint_msg.coordinate_frame = mavros_msgs::PositionTarget::FRAME_LOCAL_NED;
        setpoint_msg.type_mask = mavros_msgs::PositionTarget::IGNORE_YAW_RATE | mavros_msgs::PositionTarget::IGNORE_VZ | mavros_msgs::PositionTarget::IGNORE_AFZ;

        // Set position
        setpoint_msg.position.x = -(r * c * s) / sspo;
        setpoint_msg.position.y = (r * c) / sspo;
        setpoint_msg.position.z = FLIGHT_ALTITUDE;
        
        // Set velocity
        setpoint_msg.velocity.x = dadt * r * (ss * ss + ss + (ssmo * cc)) / sspos;
        setpoint_msg.velocity.y = -dadt * r * s * (ss + 2.0 * cc + 1.0) / sspos;
        
        // Set acceleration
        setpoint_msg.acceleration_or_force.x = -dadt * dadt * 8.0 * r * s * c * ((3.0 * c2a) + 7.0) / c2am3_cubed;
        setpoint_msg.acceleration_or_force.y = dadt * dadt * r * c * ((44.0 * c2a) + c4a - 21.0) / c2am3_cubed;
        
        // Set yaw (in radians)
        setpoint_msg.yaw = atan2(setpoint_msg.velocity.y, setpoint_msg.velocity.x);

        setpoint_pub.publish(setpoint_msg);

        ros::spinOnce();
        rate.sleep();
    }

    return 0;
}
```

