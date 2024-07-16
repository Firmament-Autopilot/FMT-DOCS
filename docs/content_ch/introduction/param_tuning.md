## 参数调整
预设的控制参数可能不适合您的具体飞行器，因此需要根据您的需求手动微调这些参数。

### 读取参数
要访问控制参数，只需在控制台中输入 `param list CONTROL`。该命令将显示所有控制参数及其当前值的列表。

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
此外，如果您将某个参数从其默认值更改了，列表中该参数的开头会显示一个 `*` 符号。要显示已更改的参数，可以使用 `-c` 选项；要显示默认值，可以使用 `-d` 选项。

### 修改参数
推荐的方法是使用 `param` 命令修改参数。例如，要将 ROLL 的角速度参数 P 设置为 0.1，可以使用以下命令：
```
param set ROLL_RATE_P 0.1
```
修改参数后，无需重新启动飞控器，修改会立即生效。因此理论上，您可以在飞行过程中进行参数微调。然而，出于安全考虑，建议在起飞前在地面上调整参数。
调整一组控制效果稳定的参数后，请记得输入 `param save` 保存它们，以防止在断电时丢失参数。

您也可以使用 QGC 来修改参数。只需导航到 QGC 的参数页面，输入您想修改的参数，然后点击它来调整其数值。

<p align="center">
  <img src="figures/qgc_parameter.png" width="70%">
</p>

### 检查控制数据
有多种方法可以检查控制数据，我们这里介绍其中两种。

#### 在线数据查看
第一种方法是通过 QGroundControl (QGC) 进行实时数据检查。您可以将数据打包成 MAVLink 消息，使用 ID `MAVLINK_MSG_ID_DEBUG_FLOAT_ARRAY` 发送到 QGC。通过这种方式，您可以在 QGC 的分析页面上访问您的数据，并方便地查看。
<p align="center">
 <img src="figures/check_data.png" width="70%">
</p>

以下是发送调试数据的示例代码：
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

#### 离线数据查看

第二种方法是通过数据仿真进行离线数据查看，这是一种高度推荐的方法。通过这种方法，您可以获取所有需要进行分析和测试的数据。有关更多详细信息，请参阅[数据仿真](http://localhost:3000/#/content_ch/simulation/openloop)部分。

<p align="center">
 <img src="./figures/sdi_signal.png" width="70%">
</p>