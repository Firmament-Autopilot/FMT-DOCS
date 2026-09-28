# Mavproxy

## 介绍

Mavproxy作为一个接口，用于建立地面控制站（GCS）或机载计算机（OBC）与飞行器之间的通信，使用MAVLink协议。它能够注册MAVLink消息，这些消息可以周期性地发送或作为一次性传输。此外，所有传入的MAVLink消息都由mavproxy管理。mavproxy的多功能性允许通过多种设备进行数据的传输和接收，包括串口和USB连接。

mavproxy具有支持多个通道的能力，目前支持两个通道。通道0（MAVPROXY_GCS_CHAN）专门用于地面控制站（GCS），而通道1（MAVPROXY_OBC_CHAN）则专门用于机载计算机（OBC）。这种设置使得mavproxy能够有效地同时处理GCS和OBC之间的通信。

## 接口函数

```c
fmt_err_t mavproxy_init(void);
void mavproxy_loop(void);
mavlink_system_t mavproxy_get_system(void);
fmt_err_t mavproxy_set_channel(uint8_t chan);
fmt_err_t mavproxy_send_event(uint32_t event_set);
fmt_err_t mavproxy_send_immediate_msg(const mavlink_message_t* msg, bool sync);
fmt_err_t mavproxy_register_period_msg(uint8_t msgid, uint16_t period_ms, msg_pack_cb_t msg_pack_cb, uint8_t enable);
```

## 发送消息

发送 MAVLink 消息时，你可以使用函数 `mavproxy_send_immediate_msg(uint8_t chan, const mavlink_message_t* msg, bool sync)`。

当 `sync=true` 时，该函数只有在消息成功发送后才会返回。

相反，当 `sync=false` 时，函数会立即将消息添加到 mavproxy 发送缓冲区中，并立即返回，将消息传输的责任交给 mavproxy，后者会异步处理消息的发送。

```
mavproxy_send_immediate_msg(MAVPROXY_GCS_CHAN, &msg, false);	// asynchronous send
mavproxy_send_immediate_msg(MAVPROXY_GCS_CHAN, &msg, true);		// synchronous send
```

你可以使用函数 `mavproxy_register_period_msg(uint8_t chan, uint8_t msgid, uint16_t msg_rate_hz, msg_pack_cb_t msg_pack_cb, bool start)` 来注册周期性消息。这样可以让消息以指定的频率自动发送出去。例如：

```c
mavproxy_register_period_msg(MAVPROXY_OBC_CHAN, MAVLINK_MSG_ID_HEARTBEAT, 1, mavlink_msg_heartbeat_pack_func, true);
```

通过使用 `mavproxy_register_period_msg(uint8_t chan, uint8_t msgid, uint16_t msg_rate_hz, msg_pack_cb_t msg_pack_cb, bool start)` 函数，你可以注册heartbeat MAVLink 消息，使其以 1Hz 的频率发送。为此，你需要提供一个回调函数 `msg_pack_cb`，该函数负责在消息发送之前打包填充消息的数据。
```c
bool mavlink_msg_heartbeat_pack_func(mavlink_message_t* msg_t)
{
    mavlink_heartbeat_t heartbeat = { 0 };
    FMS_Out_Bus fms_out;

    heartbeat.type = MAV_TYPE_QUADROTOR;
    heartbeat.autopilot = MAV_AUTOPILOT_PX4;
    heartbeat.base_mode = MAV_MODE_FLAG_CUSTOM_MODE_ENABLED;
    heartbeat.custom_mode = 0;
    heartbeat.system_status = MAV_STATE_STANDBY;

    if (mcn_copy_from_hub(MCN_HUB(fms_output), &fms_out) != FMT_EOK) {
        return false;
    }

    if (fms_out.status == VehicleStatus_Arm || fms_out.status == VehicleStatus_Standby) {
        heartbeat.base_mode |= MAV_MODE_FLAG_SAFETY_ARMED;
        heartbeat.system_status = MAV_STATE_ACTIVE;
    }
    /* map fms mode to px4 ctrl mode */
    heartbeat.custom_mode = get_custom_mode(fms_out);

    mavlink_msg_heartbeat_encode(mavlink_system.sysid, mavlink_system.compid, msg_t, &heartbeat);

    return true;
}
```

## 处理消息

所有的 MAVLink 消息都在 `handle_mavlink_msg()` 函数（位于 `mavproxy_monitor.c` 中）中处理。你可以在这里添加需要处理的额外 MAVLink 消息。请注意，为了避免阻塞其他消息，请不要在这里执行耗时的操作。

MAVProxy 监视器提供了一个函数 `mavproxy_monitor_register_handler(uint8_t chan, fmt_err_t (*handler)(mavlink_message_t*, mavlink_system_t))`，允许你为指定的通道注册一个 MAVLink 消息处理器。通过这样做，你可以在注册的处理器中处理所有的 MAVLink 消息。以下是一个示例供参考：

```
/* register gcs mavlink handler */
FMT_TRY(mavproxy_monitor_register_handler(MAVPROXY_GCS_CHAN, handle_mavlink_message));
```

```
static fmt_err_t handle_mavlink_message(mavlink_message_t* msg, mavlink_system_t this_system)
{
    switch (msg->msgid) {
    case MAVLINK_MSG_ID_HEARTBEAT:
        gcs_cmd_heartbeat();
        break;

    case MAVLINK_MSG_ID_SYSTEM_TIME:
        /* do nothing */
        break;

    default:
        LOG_W("unhandled mavlink msg:%d", msg->msgid);
        return FMT_ENOTHANDLE;
    }

    return FMT_EOK;
}
```

