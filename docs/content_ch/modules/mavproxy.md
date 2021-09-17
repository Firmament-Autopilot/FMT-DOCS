# Mavproxy

## 介绍

Mavproxy 提供了接口函数基于 mavlink 协议来跟地面站进行通信。可以通过注册一个周期消息或者即时消息来进行 mavlink 包的发送。所有收到的 mavlink 消息也是由 mavproxy 来进行处理。Mavproxy 可以被配置为使用任意的设备来收发数据，比如串口或者 USB。

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

你可以使用函数 `mavproxy_send_immediate_msg(const mavlink_message_t* msg, bool sync)` 来发送单条 mavlink 消息。如果 `sync=true`，该函数仅在消息发送完成后再返回。

你也可以注册周期消息。周期消息会以固定周期来发送数据，例如：

```c
mavproxy_register_period_msg(MAVLINK_MSG_ID_ATTITUDE, 100,
        mavproxy_msg_attitude_pack, 1);
```

这里注册了一个 attitude 的 mavlink 消息并以 10Hz 进行发送。你需要提供一个回调函数，在数据发送之前负责对消息数据进行填充。

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

## 处理消息

所有的 mavlink 消息都是在 `handle_mavlink_msg()` 函数中进行处理 (*mavproxy_monitor.c*)。你可以在这里添加任意你需要被处理的 mavlink 消息。注意不要在这里执行耗时的工作以免阻塞其它消息的处理。
