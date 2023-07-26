## Parameter Tuning

The preset control parameters might not be suitable for your specific vehicle, thus requiring you to manually fine-tune the parameters according to your needs.

#### Read Parameters

To access the control parameters, simply enter `param list CONTROL` in the console. This command will display a list of all control parameters along with their current values.

```
msh />param list CONTROL
CONTROL:
   [x]                 AIRFRAME: 1
                       VEL_XY_P: 1.400000
                       VEL_XY_I: 0.200000
                       VEL_XY_D: 0.050000
                        VEL_Z_P: 0.500000
                        VEL_Z_I: 0.120000
                        VEL_Z_D: 0.000000
                   VEL_XY_I_MIN: -1.000000
                   VEL_XY_I_MAX: 1.000000
                   VEL_XY_D_MIN: -1.000000
                   VEL_XY_D_MAX: 1.000000
                    VEL_Z_I_MIN: -0.200000
                    VEL_Z_I_MAX: 0.200000
                    VEL_Z_D_MIN: -0.100000
                    VEL_Z_D_MAX: 0.100000
                         ROLL_P: 7.000000
                        PITCH_P: 7.000000
             ROLL_PITCH_CMD_LIM: 0.523599
                    ROLL_RATE_P: 0.045000
                   PITCH_RATE_P: 0.045000
                     YAW_RATE_P: 0.150000
                    ROLL_RATE_I: 0.050000
                   PITCH_RATE_I: 0.050000
                     YAW_RATE_I: 0.150000
                    ROLL_RATE_D: 0.001500
                   PITCH_RATE_D: 0.001500
                     YAW_RATE_D: 0.001000
                     RATE_I_MIN: -0.100000
                     RATE_I_MAX: 0.100000
                     RATE_D_MIN: -0.100000
                     RATE_D_MAX: 0.100000
                    P_Q_CMD_LIM: 1.570796
                      R_CMD_LIM: 3.141593
                     HOVER_THRO: 0.500000
```

Additionally, if you have altered a parameter from its default value, a `*` signal will be shown at the beginning of that particular parameter in the list. To display changed parameters, you can include the `-c` option, and to show the default values, you can use the `-d` option.

```
msh />param list CONTROL -c -d
CONTROL:
[*]                 ROLL_RATE_P: 0.050000 [0.045000]
```

#### Modify Parameters

The recommended method for modifying parameters is by using the `param` command. For example, to set the angular velocity parameter P for ROLL to 0.1, you can use the following command:

```
param set ROLL_RATE_P 0.1
```

After modifying the parameters, there is no need to restart the flight controller as the changes take effect immediately. Therefore, in theory, you can fine-tune the parameters while the aircraft is in flight. However, for safety reasons, it is advisable to adjust the parameters on the ground before taking off.

After adjusting a set of stable parameters, don't forget to enter `param save` to preserve them and prevent parameter loss in case of power failure.

You can also modify parameters using QGC. Simply navigate to the QGC parameter page, enter the parameter you wish to modify, and then click on it to adjust its value.

<p align="center">
	<img src="./figures/qgc_parameter.png" width="30%">
</p>

#### Check Control Data

There are multiple methods to check control data, and we will introduce two of them here.

##### Online Data Viewing

The first method involves real-time data checking through QGroundControl (QGC). You can pack your data into a MAVLink message with ID `MAVLINK_MSG_ID_DEBUG_FLOAT_ARRAY` and send it to QGC. By doing this, you can access your data on the QGC analysis page and view it conveniently.

<p align="center">
	<img src="./figures/check_data.png" width="30%">
</p>

Below is an example code for sending debug data:

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

#include "module/mavproxy/mavproxy.h"
#include "module/sensor/sensor_hub.h"
#include "module/task_manager/task_manager.h"

MCN_DECLARE(sensor_imu0);

fmt_err_t task_local_init(void)
{
    return FMT_EOK;
}

void task_local_entry(void* parameter)
{
    mavlink_debug_float_array_t debug_float_array = { .array_id = 0, .name = "imu_data" };
    mavlink_message_t msg;
    mavlink_system_t mav_sys = mavproxy_get_system();

    /* main loop */
    while (1) {
        imu_data_t imu_report;
        mcn_copy_from_hub(MCN_HUB(sensor_imu0), &imu_report);
        debug_float_array.time_usec = systime_now_us();
        debug_float_array.data[0] = imu_report.gyr_B_radDs[0];
        debug_float_array.data[1] = imu_report.gyr_B_radDs[1];
        debug_float_array.data[2] = imu_report.gyr_B_radDs[2];
        debug_float_array.data[3] = imu_report.acc_B_mDs2[0];
        debug_float_array.data[4] = imu_report.acc_B_mDs2[1];
        debug_float_array.data[5] = imu_report.acc_B_mDs2[2];

        mavlink_msg_debug_float_array_encode(mav_sys.sysid, mav_sys.compid, &msg, &debug_float_array);
        mavproxy_send_immediate_msg(MAVPROXY_GCS_CHAN, &msg, false);

        sys_msleep(20);
    }
}

TASK_EXPORT __fmt_task_desc = {
    .name = "local",
    .init = task_local_init,
    .entry = task_local_entry,
    .priority = 25,
    .auto_start = true,
    .stack_size = 4096,
    .param = NULL,
    .dependency = NULL
};
```

##### Offline Data Viewing

The second method involves offline data viewing through Data Simulation, which is a highly recommended approach. By using this method, you can access all the data you need for analysis and testing purposes. For further details, please refer to the [Data Simulation](simulation/DataSIM.md) section.
