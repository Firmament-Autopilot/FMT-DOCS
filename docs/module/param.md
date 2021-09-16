# Param

## Introduction

The parameter module provides functions to define, read and store parameters. The parameters can be stored in any storage device, such sd card or flash. The parameter is load from `/sys/param.xml` in system boot stage. If no parameter file existed, the default parameter value will be used.

## API

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

## Add New Parameter

Parameters are organized in units of groups. Each group contains one or more parameters. To add a new parameter, you must first select a group that it belongs to. You can also create a new group by following steps:

- Add a new group to `param_list`. e.g

```c
param_list_t param_list = { 
    ......
    PARAM_DEFINE_GROUP(my_group),
};
```

- Declare the new group.

```c
typedef struct {
	......
	param_group_t	PARAM_GROUP(my_group);
} param_list_t;
```

Then you can add parameters into the group:

- Add new parameters.

```c
PARAM_GROUP(my_group) PARAM_DECLARE_GROUP(my_group) = \
{
	PARAM_DEFINE_FLOAT(my_param1, 0.5),
	PARAM_DEFINE_UINT32(my_param2, 1),
};
```

- Declare new parameters.

```c
typedef struct {
	PARAM_DECLARE(my_param1);
	PARAM_DECLARE(my_param2);
} PARAM_GROUP(my_group);
```

## Read Parameter

The parameter module provides the following macros to quickly read the value of a parameter (non-query mode). The user needs to select the macro which matches the parameter type by giving the group and parameter name.

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

The following functions can also be used to get the parameter's value. However it is relatively slow when too many parameters are defined. So try to use macros to read parameter values.

```c
param_t* param_get_by_name(const char* param_name);
param_t* param_get_by_full_name(const char* group_name, const char* param_name);
param_t* param_get_by_index(int16_t index);
```

## Set Parameter

Similarly, the parameter module provides the macros to quickly set the value of a parameter.

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

The following functions are provided to set the parameter's value with slower speed.

```c
fmt_err_t param_set_val(param_t* param, void* val);
fmt_err_t param_set_val_by_name(char* param_name, void* val);
fmt_err_t param_set_val_by_full_name(char* group_name, char* param_name, void* val);
fmt_err_t param_set_string_val(param_t* param, char* val);
fmt_err_t param_set_string_val_by_name(char* param_name, char* val);
fmt_err_t param_set_string_val_by_full_name(char* group_name, char* param_name, char* val);
```

> Note that you need call `param_save(path)` to save parameters after value changed.

## Command

```
usage: param <command> [options]

command:
 list        List groups and parameters.
 set         Set parameter.
 get         Get parameter.
 save        Save parameters to file.
 load        Load parameters from file.
```
