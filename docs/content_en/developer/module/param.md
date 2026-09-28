# Param

## Introduction

The parameter module offers functions for defining, reading, writing, and storing parameters. These parameters can be stored on various storage devices, such as an SD card or flash memory. During the system boot stage, the parameters are loaded from the `/sys/param.xml` file. In case no parameter file exists, the default parameter values will be utilized.

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

Parameters are organized into parameter groups, with each group containing one or more parameters. When adding a new parameter, you must first choose a parameter group to which the parameter belongs. You can either add the parameter to an existing parameter group or create a new parameter group specifically for it. This organization helps to manage and categorize parameters effectively.

As illustrated below, we have defined a new parameter group called `New_Group`. The `param_list` represents the list of parameters within this group. Parameters can be defined in any source file, but conventionally, they are placed at the top of the module file they are associated with for better organization.

```c
static param_t param_list[] = {
    PARAM_FLOAT(my_param1, 0.5, false),
    PARAM_UINT32(my_param2, 1, false),
};
PARAM_GROUP_DEFINE(New_Group, param_list);
```

We can define different types of parameters using the macros provided as follows:

```c
PARAM_INT8(_name, _default, _readonly)
PARAM_UINT8(_name, _default, _readonly)
PARAM_INT16(_name, _default, _readonly)
PARAM_UINT16(_name, _default, _readonly)
PARAM_INT32(_name, _default, _readonly)
PARAM_UINT32(_name, _default, _readonly)
PARAM_FLOAT(_name, _default, _readonly)
PARAM_DOUBLE(_name, _default, _readonly)
```

> During the system boot process, the default values of parameters can be overwritten by the values loaded from the `/sys/param.xml` file. This allows customization of parameter values based on the content stored in the XML file.

## Read Parameter

The parameter module offers the following macros to facilitate easy reading of parameter values. However, it's crucial for the user to utilize a macro that corresponds to the parameter type, otherwise, an incorrect value may be read. Ensuring the proper macro usage is essential to retrieve accurate parameter data.

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

Retrieving parameter values using the before mentioned method is not particularly efficient since it involves querying to find the corresponding parameters. When dealing with a large number of parameters, this querying overhead can become significant. A more efficient approach to read parameters is as follows:

```c
static float my_val;
......
param_link_variable(PARAM_GET(New_Group, my_param1), &my_val);
```

The `param_link_variable()` function establishes a link between the `my_param1` parameter and the `my_val` variable. As a result, whenever the parameter value changes, the value of `my_val` will automatically update to reflect the new parameter value.

## Set Parameter

Similarly, the parameter module offers the following macros to easily set parameter values. However, as the query method is utilized to obtain parameters, the efficiency may be relatively low.

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

A more efficient approach involves using the `PARAM_GET()` macro to find the corresponding parameter and then utilizing the `param_set_val` function to modify its value. By adopting this method, the need for repetitive querying when modifying parameters is eliminated, resulting in improved efficiency.

```
param_t* param = PARAM_GET(New_Group, my_param1);
param_set_val(param, &((float) { 3.5 }));
```

> Please be aware that after making changes to parameter values, you must call `param_save(path)` to save the updated parameters. This step ensures that the modified values are persisted and preserved for future use.

## Command

```
usage: param <command> [options]

command:
 list        List groups and parameters.
 set         Set parameter.
 get         Get parameter.
 save        Save parameters to file.
 load        Load parameters from file.
 restore	 Restore parameters to default value.
```
