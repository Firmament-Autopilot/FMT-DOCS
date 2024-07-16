# Mlog

## 介绍

MLog模块提供了一个以二进制格式记录数据地函数，作为黑匣子机制运行。同时，它保证了高带宽并满足实时需求，确保了高效和准确的数据记录。

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

> 重要提示：为确保日志数据正确记录，必须将日志总线的元素数据对齐到4字节边界。这种对齐保证了在日志记录过程中数据的正确存储和检索。

## 记录数据

要记录单个日志消息，你可以使用函数 `mlog_push_msg(const uint8_t* payload, uint8_t msg_id, uint16_t len)`，例如：

```c
    if (ins_handle.mag_updated) {
        /* Log Magnetometer data */
        if (mlog_push_msg((uint8_t*)&INS_U.MAG, MLOG_MAG_ID, sizeof(INS_U.MAG)) == FMT_EOK) {
            ins_handle.mag_updated = 0;
        }
    }
```

在这个例子中，`MLOG_MAG_ID`表示日志总线的ID，可以使用`mlog_get_bus_id("MAG")`函数获取。

通常，数据在更新后记录，以避免日志中出现重复条目，确保只记录最新的信息。

> 总线通常包含一个时间戳元素，用于将日志解析成带时间信息的 *timeseries* 数据类型,将时间信息与记录的数据结合起来。

## 开始 & 结束记录

mlog的启动和停止过程的初始化和终止由MLOG_MODE参数或mlog命令控制。这些控制确定日志操作何时开始和停止，从而方便管理数据记录。

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
- 命令行
您可以使用命令mlog start来启动记录（这不是启动日志），并使用命令mlog stop停止记录。这些命令提供了一种简单直接的方式来启动和停止数据记录过程。

## 解析日志

您可以使用[parse_mlog.m](https://github.com/Firmament-Autopilot/FMT-Model/blob/master/utils/log_parser/parse_mlog.m)脚本来解析日志文件并生成.mat文件。这些.mat文件将包含从日志中提取的数据，方便在MATLAB中进行简便的分析和进一步处理。

## 命令

```
usage: mlog <command> [options]

command:
 start       Start logging.
 stop        Stop logging.
 status      Get logging status.
```
