# Mavproxy

## Introduction

Mavproxy provides interfaces to communicate with ground station via mavlink protocol. It can register a mavlink message which is sent out periodically or just sent once. All income mavlink messages are also handled by mavproxy. The mavproxy can be configured use any device to send/receive data, such as serial and usb.

## API

```c
fmt_err_t mavproxy_init(void);
void mavproxy_loop(void);
mavlink_system_t mavproxy_get_system(void);
fmt_err_t mavproxy_set_channel(uint8_t chan);
fmt_err_t mavproxy_send_event(uint32_t event_set);
fmt_err_t mavproxy_send_immediate_msg(const mavlink_message_t* msg, bool sync);
fmt_err_t mavproxy_register_period_msg(uint8_t msgid, uint16_t period_ms, msg_pack_cb_t msg_pack_cb, uint8_t enable);
```

## Send Message

To send a mavlink message, you can use function `mavproxy_send_immediate_msg(const mavlink_message_t* msg, bool sync)`. If `sync=true`, then this function will only return when the message has been sent out.

You can also register a period message, which will be automatically sent out with a given period. e.g.

```c
mavproxy_register_period_msg(MAVLINK_MSG_ID_ATTITUDE, 100,
        mavproxy_msg_attitude_pack, 1);
```

This will register an attitude mavlink message which is sent at 10Hz. You need provide a callback function which is used to fill in the message data before the message is sent.

```c
static bool mavproxy_msg_attitude_pack(mavlink_message_t* msg_t)
{
    mavlink_attitude_t attitude;
    INS_Out_Bus ins_out;

    mcn_copy_from_hub(MCN_HUB(ins_output), &ins_out);

    attitude.roll = ins_out.phi;
    attitude.pitch = ins_out.theta;
    attitude.yaw = ins_out.psi;
    attitude.rollspeed = ins_out.p;
    attitude.pitchspeed = ins_out.q;
    attitude.yawspeed = ins_out.r;

    mavlink_msg_attitude_encode(mavlink_system.sysid, mavlink_system.compid,
        msg_t, &attitude);

    return true;
}
```

## Handle Message

All mavlink messages are handled in `handle_mavlink_msg()` function (*mavproxy_monitor.c*). You can add extra mavlink message here that need be handled. Note that don't perform time-consuming works here to avoid blocking other messages.
