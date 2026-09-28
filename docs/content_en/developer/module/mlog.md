# MLog

## Introduction

The MLog module offers a function to record data in binary format, operating as a black-box mechanism. Simultaneously, it guarantees high bandwidth and meets real-time requirements, ensuring efficient and accurate data logging.

## API

```c
int mlog_get_bus_id(const char* bus_name);
fmt_err_t mlog_add_desc(char* desc);
fmt_err_t mlog_start(char* file_name);
void mlog_stop(void);
fmt_err_t mlog_push_msg(const uint8_t* payload, uint8_t msg_id, uint16_t len);
uint8_t mlog_get_status(void);
char* mlog_get_file_name(void);
void mlog_show_statistic(void);
fmt_err_t mlog_register_callback(mlog_cb_type type, void (*cb_func)(void));
fmt_err_t mlog_deregister_callback(mlog_cb_type type, void (*cb_func)(void));
fmt_err_t mlog_init(void);
void mlog_async_output(void);
```

## Add New Data

Mlog is structured in units of buses, and each bus comprises one or more elements. Each element represents individual log information. To record new data, you can either add new elements to an existing bus or create an entirely new bus.

For example, you can create a bus named **MAG** that contains elements representing magnetometer values.

```c
static mlog_elem_t MAG_Elems[] = {
    MLOG_ELEMENT(timestamp, MLOG_UINT32),
    MLOG_ELEMENT(mag_x, MLOG_FLOAT),
    MLOG_ELEMENT(mag_y, MLOG_FLOAT),
    MLOG_ELEMENT(mag_z, MLOG_FLOAT),
};
MLOG_BUS_DEFINE(MAG, MAG_Elems);
```

You can also add vector type of data with macro `MLOG_ELEMENT_VEC(_name, _type, _num)`.

```c
mlog_elem_t MAG_Elems[] = {
    MLOG_ELEMENT("timestamp", MLOG_UINT32),
    MLOG_ELEMENT_VEC("mag", MLOG_FLOAT, 3),
};
MLOG_BUS_DEFINE(MAG, MAG_Elems);
```

> Important Note: To ensure correct recording of log data, it is essential to align the element data of the log bus on 4-byte boundaries. This alignment guarantees proper data storage and retrieval during the logging process.

## Record Data

To record a single log message, you can use `mlog_push_msg(const uint8_t* payload, uint8_t msg_id, uint16_t len)` function. For example:

```c
    if (ins_handle.mag_updated) {
        /* Log Magnetometer data */
        if (mlog_push_msg((uint8_t*)&INS_U.MAG, MLOG_MAG_ID, sizeof(INS_U.MAG)) == FMT_EOK) {
            ins_handle.mag_updated = 0;
        }
    }
```

In this example, `MLOG_MAG_ID` represents the ID of the log bus, which can be retrieved using the `mlog_get_bus_id("MAG")` function.

Typically, data is recorded after it is updated to avoid duplicate entries in the log, ensuring that only the latest information is logged.

> The bus commonly includes a timestamp element, specifically used to parse the log into a *timeseries* data type, incorporating valuable time information along with the recorded data.

## Start & Stop Recording

The initiation and termination of the mlog start/stop process are governed by either the `MLOG_MODE` parameter or the **mlog** command. These controls determine when the logging operation begins and halts, allowing for convenient management of data recording.

- MLOG_MODE

    ```c
    PARAM_DECLARE_GROUP(SYSTEM) = {
        /* Determines when to start and stop logging.
        0: disabled
        1: when armed until disarm
        2: from boot until disarm
        3: from boot until shutdown  */
        PARAM_DEFINE_INT32(MLOG_MODE, 0),
    };
    ```

- Command

	Alternatively, you have the option to start logging by using the command `mlog start` (This is not boot log), and to stop logging, you can utilize the command `mlog stop`. These commands provide a simple and straightforward way to initiate and halt the data logging process.

## Parse log

You can utilize the [parse_mlog.m](https://github.com/Firmament-Autopilot/FMT-Model/blob/master/utils/log_parser/parse_mlog.m) script to parse the log file and generate .mat files. These .mat files will contain the data extracted from the log, allowing for easy analysis and further processing in MATLAB.

## Command

```
usage: mlog <command> [options]

command:
 start       Start logging.
 stop        Stop logging.
 status      Get logging status.
```
