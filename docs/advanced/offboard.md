## Offboard Control

> Offboard control is dangerous. It is the responsibility of the developer to ensure adequate preparation, testing and safety precautions are taken before offboard flights.

Offboard mode is used for controlling vehicle movement and attitude, by setting position, velocity, acceleration, attitude, attitude rates or thrust/torque setpoints.

The idea behind off-board control is to be able to control the FMT flight stack using software running outside of the autopilot. This is done through the MAVLink protocol, specifically the [SET_POSITION_TARGET_LOCAL_NED](https://mavlink.io/en/messages/common.html#SET_POSITION_TARGET_LOCAL_NED) and the [SET_ATTITUDE_TARGET ](https://mavlink.io/en/messages/common.html#SET_ATTITUDE_TARGET) messages.

The standard procedure for offboard control is as follows:

1. Activate offboard control mode.
2. Issue a takeoff command to initiate ascent to the designated altitude.
3. Once airborne, the vehicle will transition into a hold mode since no offboard commands have been dispatched yet.
4. Initiate the offboard task within the onboard computer to transmit commands to the flight controller.

> You can utilize the [SIH](simulation/SIH.md) simulation mode to rehearse offboard control procedures prior to conducting real-world testing.

### MAVROS Control

Currently, FMT provides support for most of the message functionalities in MAVROS and has been tested successfully on ROS Melodic. The following section presents the basic usage instructions for utilizing the FMT MAVROS features. All examples given are based on the ICF5 platform, and similar configurations can be applied to other hardware platforms.

#### Communication Interface Configuration

First, you need to configure the `sysconfig.toml` file on the flight controller to set up the communication interface between the flight controller and the onboard computer. You'll need to configure `mavproxy` as shown below. In this configuration, three devices are defined: two devices on channel 0 (`chan=0`) are used for communication with the ground station, and one device on channel 1 (`chan=1`) is used for communication with the onboard computer. Therefore, this configuration uses `serial2`, which corresponds to the TELEM2 port on the flight controller, for communication with the onboard computer.

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

If you want to use USB for communication with the onboard computer, you can configure it as follows.

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

> After modifying the `sysconfig.toml` file, you need to upload it to the flight controller's onboard file system under the `/sys` directory and then restart the flight controller for the changes to take effect.

#### Launch MAVROS

First, establish a connection between the flight controller's serial port or USB and the onboard computer. Assuming the onboard computer runs Ubuntu, a corresponding device should appear in Ubuntu's /dev directory, such as /dev/ttyACM0.

 <p align="center">
 	<img src="./figures/ttyacm0.png" width="30%">
 </p>

Modify the `px4.launch` file in MAVROS to make the device port and baud rate consistent with device configuration.

 <p align="center">
 	<img src="./figures/px4_launch.png" width="50%">
 </p>

Then, use the following command to run MAVROS:

```
roslaunch mavros px4.launch
```

A successful start of MAVROS should result in seeing the message `Got HEARTBEAT`.

 <p align="center">
 	<img src="./figures/mavros.png" width="50%">
 </p>
 #### Control Vehicle From Onboard Computer

Currently, FMT has already supported the following MAVLink messages for onboard computers. If you require support for other messages, please inform us for evaluation, or you can modify the code in `mavobc.c` to add handling for the corresponding MAVLink messages.

- Mode setting
- Arming/Disarming
- Takeoff/Landing/Return-to-Home/Hold/Pause/Continue commands support
- MAVLINK_MSG_ID_SET_ATTITUDE_TARGET (Supporting coordinate: FRAME_BODY_FRD)
- MAVLINK_MSG_ID_SET_POSITION_TARGET_LOCAL_NED (Supporting coordinates: MAV_FRAME_LOCAL_NED, MAV_FRAME_LOCAL_FRD, MAV_FRAME_BODY_FRD)
- MAVLINK_MSG_ID_SET_POSITION_TARGET_GLOBAL_INT (Supporting coordinate: MAV_FRAME_GLOBAL_INT)

Here are some test ROS commands. You can also use C++ or Python to call ROS interfaces, and the results will be the same.

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

> Please note that setting the mode through the onboard computer is only possible when the remote controller is in the OFF state. This is because the remote controller's mode takes precedence over the ground station and onboard computer.

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

Sending target position commands is effective only when the vehicle is in **Offboard** mode. If the Offboard mode is entered, but no control signal commands are sent to the flight controller (i.e., the publication frequency of `auto_cmd` messages is 0), the vehicle will enter Hold mode, hovering in place.

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

> Please note that the coordinate system used in ROS (ENU) is different from the one used in the flight controller (NED). Mavros will perform coordinate transformations to account for this difference.

After sending this message, the flight controller will receive the `auto_cmd` message. You can then input `mcn echo auto_cmd` on the flight controller console to print the output.

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

Similarly, you can use the following commands to send target attitude command.

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

To print the received messages on the flight controller, input `mcn echo auto_cmd` on the flight controller's console.

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

#### Configure Messages Sent to Onboard Computer

By default, FMT will only send the Heartbeat message (1Hz) to the onboard computer. Upon receiving the Heartbeat message from the flight controller, the onboard computer needs to send the MAVLINK_MSG_ID_REQUEST_DATA_STREAM message to the flight controller to configure the messages it wants to receive.

FMT supports the following onboard computer messages. If you require support for other messages, please inform us for evaluation, or you can modify the code in `mavobc.c` to add the corresponding MAVLink messages sending functionality.

 <p align="center">
 	<img src="./figures/mavobc.png" width="50%">
 </p>

You can use the following ros example code to configure the messages and their transmission rate sent by the flight controller.

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

### Onboard Control

The offboard control command can also be sent through an onboard task. Here is an example demonstrating how to send the 8-figure command to control the drone to follow an 8-figure path.

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
 	<img src="./figures/eight_figure.jpg" width="100%">
 </p>

