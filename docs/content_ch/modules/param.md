# Param

## 介绍

参数模块提供了参数的定义、读取和存储的功能。参数可以被存储在任意的存储设备，比如 sd 卡，flash等。在系统启动阶段，参数从 `/sys/param.xml` 进行加载。如果参数文件不存在，则使用默认参数。

## 接口函数

```c
fmt_err_t param_init(void);
fmt_err_t param_save(char* path);
fmt_err_t param_load(char* path);
fmt_err_t param_set_val(param_t* param, void* val);
fmt_err_t param_set_val_by_name(char* param_name, void* val);
fmt_err_t param_set_val_by_full_name(char* group_name, char* param_name, void* val);
fmt_err_t param_set_string_val(param_t* param, char* val);
fmt_err_t param_set_string_val_by_name(char* param_name, char* val);
fmt_err_t param_set_string_val_by_full_name(char* group_name, char* param_name, char* val);
uint32_t param_get_count(void);
int16_t param_get_index(const param_t* param);
param_t* param_get_by_name(const char* param_name);
param_t* param_get_by_full_name(const char* group_name, const char* param_name);
param_t* param_get_by_index(int16_t index);
param_group_t* param_find_group(const char* group_name);
```

## 添加新参数

参数使用组 (group) 来进行组织。每个组包含一个或者多个参数。在添加新参数之前，你必须先为该参数选定一个组。你可以按照如下步骤来建立一个新的组：

- 在 `param_list` 添加新的组。例如：

```c
param_list_t param_list = { 
    ......
    PARAM_DEFINE_GROUP(my_group),
};
```

- 申明新的组.

```c
typedef struct {
	......
	param_group_t	PARAM_GROUP(my_group);
} param_list_t;
```

然后你可以往该组里面加入参数：

- 添加新参数

```c
PARAM_GROUP(my_group) PARAM_DECLARE_GROUP(my_group) = \
{
	PARAM_DEFINE_FLOAT(my_param1, 0.5),
	PARAM_DEFINE_UINT32(my_param2, 1),
};
```

- 申明新参数.

```c
typedef struct {
	PARAM_DECLARE(my_param1);
	PARAM_DECLARE(my_param2);
} PARAM_GROUP(my_group);
```

## 读取参数

参数模块提供了如下的宏来快速读取参数值 (非查询模式)。用户需要选择符合参数类型的宏并给定参数的组名和参数名。

```c
#define PARAM_GET_INT8(_group, _name)
#define PARAM_GET_UINT8(_group, _name)
#define PARAM_GET_INT16(_group, _name)
#define PARAM_GET_UINT16(_group, _name)
#define PARAM_GET_INT32(_group, _name)
#define PARAM_GET_UINT32(_group, _name)
#define PARAM_GET_FLOAT(_group, _name)
#define PARAM_GET_DOUBLE(_group, _name)
```

如下函数也可以用来读取参数的值。但是相比而言当参数量大时速度会比较慢，因为这些函数使用查询模式。所以请尽量使用宏来读取参数值。

```c
param_t* param_get_by_name(const char* param_name);
param_t* param_get_by_full_name(const char* group_name, const char* param_name);
param_t* param_get_by_index(int16_t index);
```

## 设置参数

类似的，参数模块也提供了如下宏来快速设置参数值。

```c
#define PARAM_SET_INT8(_group, _name, _val)
#define PARAM_SET_UINT8(_group, _name, _val)
#define PARAM_SET_INT16(_group, _name, _val)
#define PARAM_SET_UINT16(_group, _name, _val)
#define PARAM_SET_INT32(_group, _name, _val)
#define PARAM_SET_UINT32(_group, _name, _val)
#define PARAM_SET_FLOAT(_group, _name, _val)
#define PARAM_SET_DOUBLE(_group, _name, _val)
```

如下函数也可以用来设置参数值但是速度更慢。

```c
fmt_err_t param_set_val(param_t* param, void* val);
fmt_err_t param_set_val_by_name(char* param_name, void* val);
fmt_err_t param_set_val_by_full_name(char* group_name, char* param_name, void* val);
fmt_err_t param_set_string_val(param_t* param, char* val);
fmt_err_t param_set_string_val_by_name(char* param_name, char* val);
fmt_err_t param_set_string_val_by_full_name(char* group_name, char* param_name, char* val);
```

> 注意，当你修改了参数值后，需要使用 `param_save(path)` 来存储参数。

## 命令

```
usage: param <command> [options]

command:
 list        List groups and parameters.
 set         Set parameter.
 get         Get parameter.
 save        Save parameters to file.
 load        Load parameters from file.
```
