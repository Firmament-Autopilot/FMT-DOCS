## Drivers

The FMT drivers are designed using a layered architecture pattern, and these drivers can be broadly categorized into two types: **peripheral drivers** (located in *target/bsp-name/driver* directory) and **device drivers** (located in *src/driver* directory).

### Core Architecture

FMT offers a Hardware Abstraction Layer (HAL) that provides a unified device interface (read/write/control) upwards, while defining the operation interfaces that drivers need to implement downwards. 
 <p align="center">
 	<img src="./figures/driver_arch.png" width="30%">
 </p>

The HAL establishes standardized drivers that are applicable to most target boards, although there might be unique devices that are exclusive to specific hardware configurations. In this scenario, you have the option to bypass the HAL and instead utilize uMCN publish/subscribe mechanism for message transportation.

 <p align="center">
 	<img src="./figures/driver_arch2.png" width="30%">
 </p>

### Create a Driver

We will demonstrate two approaches to create a driver: one through the HAL and the other via uMCN.

#### HAL Driver

We will illustrate the process of adding a driver using a magnetometer as an example. Initially, locate the magnetometer device within the HAL, where you can find the definitions for the magnetometer driver operations.

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

Then implement the driver operations within your driver file. Typically, you are not required to implement optional driver operations.

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

Following that, you can move forward with the registration of the magnetometer device within the system. This enables the upper layers to access your driver device through a standardized device interface.

```c
RT_TRY(hal_mag_register(&mag_dev, mag_device_name, RT_DEVICE_FLAG_RDWR, RT_NULL));
```

#### uMCN Driver

The uMCN driver implementation is straightforward; all that's required is to establish a thread for your driver, enabling periodic data retrieval and subsequent publish it.

Illustrating with the airspeed driver as a case study, the initial step involves creating a dedicated thread for it. Subsequently, you will establish a timer to generate periodic event, which in turn awaken the thread.

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

In the thread entry function, you should periodically read sensor data and then publish it.

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

