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
fmt_err_t param_set_str_val(param_t* param, char* val);
fmt_err_t param_set_str_val_by_name(char* param_name, char* val);
fmt_err_t param_set_str_val_by_full_name(char* group_name, char* param_name, char* val);
int32_t param_get_count(void);
int32_t param_get_index(const param_t* param);
param_t* param_get_by_name(const char* param_name);
param_t* param_get_by_full_name(const char* group_name, const char* param_name);
param_t* param_get_by_index(int32_t index);
param_group_t* param_get_group(const param_t* param);
param_group_t* param_find_group(const char* group_name);
param_group_t* param_get_table(void);
int16_t param_get_group_count(void);
fmt_err_t register_param_modify_callback(void (*on_modify)(param_t* param));
fmt_err_t deregister_param_modify_callback(void (*on_modify)(param_t* param));
fmt_err_t param_link_variable(param_t* param, void* var);
```

## Add New Parameter

Parameters are organized in parameter groups. Each parameter group contains one or more parameters. Before adding a new parameter, you must first select a parameter group for the parameter and add the parameter to the parameter group, or create a new parameter group for it.

As shown below, here we define a new parameter group named `New_Group`. `param_list` is the parameter list for this parameter group. Parameters can be defined in any source file, by convention, they are generally defined at the top of the module file they belong to.

```c
static param_t param_list[] = {
    PARAM_FLOAT(my_param1, 0.5),
    PARAM_UINT32(my_param2, 1),
};
PARAM_GROUP_DEFINE(New_Group, param_list);
```

We can define different types of parameters using the macros provided as follows:

```c
PARAM_INT8(_name, _default)
PARAM_UINT8(_name, _default)
PARAM_INT16(_name, _default)
PARAM_UINT16(_name, _default)
PARAM_INT32(_name, _default)
PARAM_UINT32(_name, _default)
PARAM_FLOAT(_name, _default)
PARAM_DOUBLE(_name, _default)
```

Default values of parameters may be overridden by values loaded from the `/sys/param.xml` file during system boot process.

## Read Parameter

The parameter module provides the following macros to easily read parameter values. Note that the user needs to use a macro that matches the parameter type, otherwise the wrong value will be read.

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

Obtaining parameter values in this way is not particularly efficient, because it uses the query method to find the corresponding parameters. When there are many parameters, the overhead caused by the query will be very large. A more efficient way to read parameters is as follows:

```c
static float my_val;
......
param_link_variable(PARAM_GET(New_Group, my_param1), &my_val);
```

The `param_link_variable()` function links the `my_param1` parameter with the `my_val` variable. When the parameter value changes, the value of my_val will also change accordingly.

## Set Parameter

Similarly, the parameter module also provides the following macros to easily set parameter values. Because the query method is also used to obtain parameters, the efficiency is low.

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

A more efficient way is to first find the corresponding parameter through the `PARAM_GET()` macro, and then use the `param_set_val` function to modify the parameter value. This avoids the need to query each time the parameters are modified, so the efficiency is improved.

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
