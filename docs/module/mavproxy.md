# Mavproxy

## Introduction

Mavproxy serves as an interface to establish communication between the ground control station (GCS) or onboard computer (OBC) and the vehicle using the MAVLink protocol. It enables the registration of MAVLink messages, which can be sent periodically or as one-time transmissions. Additionally, all incoming MAVLink messages are managed by mavproxy. The versatility of mavproxy allows configuration for data transmission and reception through various devices, including serial and USB connections.

Mavproxy has the capability to function with multiple channels, and at present, it supports two channels. Channel 0 (MAVPROXY_GCS_CHAN) is dedicated for the Ground Control Station (GCS), while channel 1 (MAVPROXY_OBC_CHAN) is specifically designated for the Onboard Computer (OBC). This arrangement allows mavproxy to efficiently handle communication between both GCS and OBC simultaneously.

## API

```c
/* mavproxy API */
fmt_err_t mavproxy_init(void);
void mavproxy_channel_loop(uint8_t chan);
mavlink_system_t mavproxy_get_system(void);
fmt_err_t mavproxy_set_device(uint8_t chan, uint8_t devid);
fmt_err_t mavproxy_send_event(uint8_t chan, uint32_t event_set);
fmt_err_t mavproxy_send_immediate_msg(uint8_t chan, const mavlink_message_t* msg, bool sync);
fmt_err_t mavproxy_register_period_msg(uint8_t chan, uint8_t msgid, uint16_t msg_rate_hz, msg_pack_cb_t msg_pack_cb, bool start);

/* mavproxy monitor API */
fmt_err_t mavproxy_monitor_create(void);
fmt_err_t mavproxy_monitor_register_handler(uint8_t chan, fmt_err_t (*handler)(mavlink_message_t*, mavlink_system_t));
fmt_err_t mavproxy_monitor_deregister_handler(uint8_t chan, fmt_err_t (*handler)(mavlink_message_t*, mavlink_system_t));
```

## Send Message

To send a mavlink message, you can use function `mavproxy_send_immediate_msg(uint8_t chan, const mavlink_message_t* msg, bool sync)`. If `sync=true`, then this function will only return when the message has been sent out.

To transmit a mavlink message, you can utilize the function `mavproxy_send_immediate_msg(uint8_t chan, const mavlink_message_t* msg, bool sync)`.

When `sync=true`, the function will only return once the message has been successfully sent out.

On the other hand, when `sync=false`, the function will promptly add the message to the mavproxy send buffer and return without delay, leaving the responsibility of transmitting the message to mavproxy, which will handle the transmission asynchronously.

```
mavproxy_send_immediate_msg(MAVPROXY_GCS_CHAN, &msg, false);	// asynchronous send
mavproxy_send_immediate_msg(MAVPROXY_GCS_CHAN, &msg, true);		// synchronous send
```



You can also register a periodic message using the function `mavproxy_register_period_msg(uint8_t chan, uint8_t msgid, uint16_t msg_rate_hz, msg_pack_cb_t msg_pack_cb, bool start)`. This allows the message to be automatically sent out at a specified frequency. For example:

```c
mavproxy_register_period_msg(MAVPROXY_OBC_CHAN, MAVLINK_MSG_ID_HEARTBEAT, 1, mavlink_msg_heartbeat_pack_func, true);
```

By using the `mavproxy_register_period_msg(uint8_t chan, uint8_t msgid, uint16_t msg_rate_hz, msg_pack_cb_t msg_pack_cb, bool start)` function, you can register the heartbeat MAVLink message to be sent at a rate of 1Hz. To achieve this, you must provide a callback function, `msg_pack_cb`, which will be responsible for populating the message data before it is sent out.

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

## Handle Message

All mavlink messages are handled in `handle_mavlink_msg()` function (*mavproxy_monitor.c*). You can add extra mavlink message here that need be handled. Note that don't perform time-consuming works here to avoid blocking other messages.

The mavproxy monitor offers a function called `mavproxy_monitor_register_handler(uint8_t chan, fmt_err_t (*handler)(mavlink_message_t*, mavlink_system_t))`, which allows you to register a mavlink message handler for the specified channel. By doing so, you can handle all the mavlink messages in your registered handler. Below is an example for reference:

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

