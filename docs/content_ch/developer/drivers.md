## 驱动

FMT驱动程序采用分层架构模式设计，这些驱动程序可以广泛地分为两类：**外设驱动程序**（位于*target/bsp-name/driver*目录下）和**设备驱动程序**（位于*src/driver*目录下）。

### 核心架构

FMT提供了一个硬件抽象层（HAL），向上提供统一的设备接口（读取/写入/控制），同时向下定义了驱动程序需要实现的操作接口。

 <p align="center">
 	<img src="./figures/driver_arch.png" width="30%">
 </p>

在大多数目标板上，HAL建立了适用于标准化驱动程序，尽管可能会存在一些独特的设备，这些设备专属于特定的硬件配置。在这种情况下，你可以选择绕过HAL，而是利用uMCN的发布/订阅机制进行消息传输。

 <p align="center">
 	<img src="./figures/driver_arch2.png" width="30%">
 </p>

### 创建驱动程序

我们将展示两种创建驱动程序的方法：一种通过HAL，另一种通过uMCN。

#### HAL驱动程序

我们将以添加磁力计驱动程序为例来说明这一过程。首先，在HAL中找到磁力计设备，在那里你可以找到磁力计驱动程序操作的定义。

```c
/* mag driver opeations */
struct mag_ops {
    /**
     * @brief mag configuration function (optional)
     * @param dev mag device
     * @param cfg mag configuration
    */
    rt_err_t (*mag_config)(mag_dev_t dev, const struct mag_configure* cfg);
    /**
     * @brief mag control function (optional)
     * @param dev mag device
     * @param cmd operation command
     * @param arg command argument (optional)
    */
    rt_err_t (*mag_control)(mag_dev_t dev, int cmd, void* arg);
    /**
     * @brief mag read data function
     * @param dev mag device
     * @param pos read pos, sent by upper layer. can be used to identify the data type to read, e.g, raw data or scaled data
     * @param data read data buffer. normally it's a pointer to float[3]
     * @param size read data size
    */
    rt_size_t (*mag_read)(mag_dev_t dev, rt_off_t pos, void* data, rt_size_t size);
};
```
接着，在你的驱动文件中实现驱动程序操作。通常情况下，你不需要实现可选的驱动程序操作。

```c
static rt_size_t bmm150_read(mag_dev_t mag, rt_off_t pos, void* data, rt_size_t size)
{
    if (data == RT_NULL) {
        return 0;
    }

    if (mag_measure(((float*)data)) != RT_EOK) {
        return 0;
    }

    return size;
}

const static struct mag_ops __mag_ops = {
    .mag_config = NULL,
    .mag_control = NULL,
    .mag_read = bmm150_read,
};
```

接下来，你可以继续在系统中注册磁力计设备。这样可以使上层通过标准化的设备接口访问你的驱动设备。

```c
RT_TRY(hal_mag_register(&mag_dev, mag_device_name, RT_DEVICE_FLAG_RDWR, RT_NULL));
```

#### uMCN驱动程序

uMCN驱动程序的实现非常直接；你只需为你的驱动程序建立一个线程，使其能够定期获取数据并发布它。

以空速驱动程序为例进行说明，首先是创建一个专用线程。接下来，你将建立一个定时器来生成周期性事件，唤醒线程进行操作。

```c
static void timer_update(void* parameter)
{
    rt_event_send(&event, EVENT_MS4525_UPDATE);
}

rt_err_t drv_ms4525_init(const char* i2c_device_name, const char* device_name)
{
    ......

    RT_CHECK(rt_event_init(&event, "ms4525", RT_IPC_FLAG_FIFO));

    thread = rt_thread_create("ms4525", thread_entry, RT_NULL, 2 * 1024, 7, 1);
    RT_ASSERT(thread != NULL);
    RT_CHECK(rt_thread_startup(thread));

    /* register timer event */
    rt_timer_init(&timer, "ms4525", timer_update, RT_NULL, TICKS_FROM_MS(10), RT_TIMER_FLAG_PERIODIC | RT_TIMER_FLAG_HARD_TIMER);
    if (rt_timer_start(&timer) != RT_EOK) {
        return FMT_ERROR;
    }

    return RT_EOK;
}
```

在线程的入口函数中，你应该定期读取传感器数据，然后发布它。

```c
static void thread_entry(void* args)
{
    rt_err_t res;
    rt_uint32_t recv_set = 0;
    rt_uint32_t wait_set = EVENT_MS4525_UPDATE;
    float report[2];
    airspeed_data_t airspeed_data;

    while (1) {
        /* wait event occur */
        res = rt_event_recv(&event, wait_set, RT_EVENT_FLAG_OR | RT_EVENT_FLAG_CLEAR, RT_WAITING_FOREVER, &recv_set);

        if (res == RT_EOK && (recv_set & EVENT_MS4525_UPDATE)) {
            if (ms4525_read(NULL, 0, report, 8) == 8) {
                airspeed_data.diff_pressure_pa = report[0] - diff_press_offset;
                airspeed_data.temperature_deg = report[1];
                airspeed_data.timestamp_ms = systime_now_ms();

                mcn_publish(MCN_HUB(sensor_airspeed), &airspeed_data);
            }
        }
    }
}
```

