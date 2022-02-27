# Mlog

## Introduction

Mlog module provides the function to record data in binary format, which works like a black-box.

## API

```c
fmt_err_t mlog_add_desc(char* desc);
fmt_err_t mlog_start(char* file_name);
void mlog_stop(void);
fmt_err_t mlog_push_msg(const uint8_t* payload, uint8_t msg_id, uint16_t len);
uint8_t mlog_get_status(void);
char* mlog_get_file_name(void);
void mlog_statistic(void);
fmt_err_t mlog_register_callback(uint8_t cb_type, void (*cb)(void));
fmt_err_t mlog_init(void);
void mlog_async_output(void);
```

## Add New Data

Mlog is organized in units of bus and each bus contains one or more elements. One element represents a single log information. To record new data you can add new elements into an existed bus or create a new bus.

For instance, you can create a bus of MAG which contains elements of magnetometer values.

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

> Note: To ensure that the log data can be recorded correctly, the element data of the log bus should be aligned by 4 bytes.

```c
mlog_bus_t _mlog_bus[] = {
    ......
    MLOG_BUS("MAG", MLOG_MAG_ID, MAG_Elems)
};
```

## Record Data

To record a single log message, you can use `mlog_push_msg(const uint8_t* payload, uint8_t msg_id, uint16_t len)` function. e.g.

```c
    if (ins_handle.mag_updated) {
        /* Log Magnetometer data */
        if (mlog_push_msg((uint8_t*)&INS_U.MAG, MLOG_MAG_ID, sizeof(INS_U.MAG)) == FMT_EOK) {
            ins_handle.mag_updated = 0;
        }
    }
```

Here `MLOG_MAG_ID` is the ID of the log bus, which can be obtained by the `mlog_get_bus_id("MAG")` function.

Generally, the data is recorded after the data is updated to prevent duplicate data from being recorded.

> The bus usually contains a timestamp element, which is used to parse the log into a *timeseries* data type with time information.

## Start & Stop Recording

The mlog start/stop process is controlled by `MLOG_MODE` parameter.

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

You can also use the command to start/stop mlog logging.

## Parse log

Use [parse_mlog.m](https://github.com/Firmament-Autopilot/FMT-Model/blob/master/utils/log_parser/parse_mlog.m) to parse log file and obtain .mat files.

## Command

```
usage: mlog <command> [options]

command:
 start       Start logging.
 stop        Stop logging.
 status      Get logging status.
```
