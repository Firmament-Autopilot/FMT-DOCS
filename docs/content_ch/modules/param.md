# Param

## 介绍

参数模块提供了参数的定义、读取和存储的功能。参数可以被存储在任意的存储设备，比如 sd 卡，flash等。在系统启动阶段，参数从 `/sys/param.xml` 进行加载。如果参数文件不存在，则使用默认参数值。

## 接口函数

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

## 添加新参数

参数通过参数组 (param group) 来进行组织。每个参数组包含一个或者多个参数。在添加新参数之前，你必须先为该参数选定一个参数组并将参数添加到该参数组中，或者为其创建一个新的参数组。

如下所示，这里我们定义了一个新的参数组，组名为`New_Group`。`param_list` 为该参数组的参数列表。参数可以定义在任意源文件中，是依据惯例，一般是将其定义在所属模块文件的顶部。

```c
static param_t param_list[] = {
    PARAM_FLOAT(my_param1, 0.5),
    PARAM_UINT32(my_param2, 1),
};
PARAM_GROUP_DEFINE(New_Group, param_list);
```

我们可以使用如下提供的宏来定义不同类型的参数:

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

参数的默认值可能被开机阶段从`/sys/param.xml`文件所加载的参数值所覆盖。

## 读取参数

参数模块提供了如下的宏来方便地读取参数值。注意，用户需要使用跟参数类型相匹配的宏，不然将读到错误的值。

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

用这个方式来获取参数值不是特别的高效，因为其使用查询的方式找到对应参数，当参数较多时，因为查询导致的开销将很大。一种更高效的读取参数的方法如下所示：

```c
static float my_val;
......
param_link_variable(PARAM_GET(New_Group, my_param1), &my_val);
```

`param_link_variable()`函数将`my_param1`参数与`my_val`变量连接起来，当参数值发生改变时，my_val的值也会对应改变。

## 设置参数

类似的，参数模块也提供了如下宏来方便地设置参数值。因其同样采用查询的方式来获取参数，所以效率较低。

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

更高效的方式是首先通过`PARAM_GET()`宏找到对应参数，然后再使用`param_set_val`函数来修改参数值。这样避免了每次修改参数都需要先查询，所以效率提高了。

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
