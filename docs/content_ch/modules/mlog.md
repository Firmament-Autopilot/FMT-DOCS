# Mlog

## 介绍

Mlog 模块提供了数据记录的功能。其中日志以二进制方式进行存储，类似于黑匣子的功能。

## 接口函数

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

## 添加新数据

Mlog 是以总线 (bus) 为单位进行组织，每个总线上包含一个或者多个元素（element）。单个元素代表单条日志信息。为了添加新的数据，你可以在已有总线上添加元素，或者建立新的总线。

举例来说，建立一个新的 MAG 总线，该总线包含磁力计数据元素：

```c
static mlog_elem_t MAG_Elems[] = {
    MLOG_ELEMENT(timestamp, MLOG_UINT32),
    MLOG_ELEMENT(mag_x, MLOG_FLOAT),
    MLOG_ELEMENT(mag_y, MLOG_FLOAT),
    MLOG_ELEMENT(mag_z, MLOG_FLOAT),
};
MLOG_BUS_DEFINE(MAG, MAG_Elems);
```

你也可以使用宏 `MLOG_ELEMENT_VEC(_name, _type, _num)` 来添加数组类型的数据：

```c
mlog_elem_t MAG_Elems[] = {
    MLOG_ELEMENT("timestamp", MLOG_UINT32),
    MLOG_ELEMENT_VEC("mag", MLOG_FLOAT, 3),
};
MLOG_BUS_DEFINE(MAG, MAG_Elems);
```

> 注意：为保证日志数据可以正确记录，日志总线的元素数据应该按4字节进行对齐。

## 记录数据

要记录单条数据，你可以使用函数 `mlog_push_msg(const uint8_t* payload, uint8_t msg_id, uint16_t len)`，例如：

```c
    if (ins_handle.mag_updated) {
        /* Log Magnetometer data */
        if (mlog_push_msg((uint8_t*)&INS_U.MAG, MLOG_MAG_ID, sizeof(INS_U.MAG)) == FMT_EOK) {
            ins_handle.mag_updated = 0;
        }
    }
```

这里`MLOG_MAG_ID`为该条日志总线的ID，可以通过`mlog_get_bus_id("MAG")`函数进行获取。

记录日志数据一般是在数据更新之后，以防止记录重复的数据。

> 总线通常包含一个时间戳元素，用于将日志解析成带时间信息的 *timeseries* 数据类型。

## 开始 & 结束记录

Mlog 的开始/结束流程由 `MLOG_MODE` 参数进行控制。

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

你也可以使用命令来开始或者结束日志的记录。

## 解析日志

使用 [parse_mlog.m](https://github.com/Firmament-Autopilot/FMT-Model/blob/master/utils/log_parser/parse_mlog.m) 脚本解析日志并获得 .mat 文件。

## 命令

```
usage: mlog <command> [options]

command:
 start       Start logging.
 stop        Stop logging.
 status      Get logging status.
```
