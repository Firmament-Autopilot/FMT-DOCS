# Param

## 介绍

参数模块提供了定义、读取、写入和存储参数的功能。这些参数可以存储在各种存储设备上，例如SD卡或闪存。在系统启动阶段，参数从/sys/param.xml文件中加载。如果没有参数文件存在，则将使用默认的参数值。

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

参数被组织成参数组，每个组包含一个或多个参数。当添加一个新参数时，您必须首先选择该参数所属的参数组。您可以将参数添加到现有的参数组中，也可以为其创建一个新的参数组。这种组织有助于有效地管理和分类参数。

如下所示，我们定义了一个名为New_Group的新参数组。param_list表示此组中的参数列表。参数可以在任何源文件中定义，但通常为了更好地组织，它们会放置在与其关联的模块文件的顶部。

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

> 在系统启动过程中，参数的默认值可以被从/sys/param.xml文件加载的值覆盖。这允许基于XML文件中存储的内容来定制参数值。

## 读取参数

参数模块提供了以下宏来便于读取参数值。然而，用户必须使用与参数类型相对应的宏，否则可能会读取到不正确的值。确保正确使用宏是获取准确参数数据的关键。

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

使用上述提到的方法来检索参数值并不特别高效，因为它涉及到查询以找到相应的参数。当处理大量参数时，这种查询的开销可能变得很大。更有效的读取参数的方法如下所示：

```c
static float my_val;
......
param_link_variable(PARAM_GET(New_Group, my_param1), &my_val);
```

函数`param_link_variable()`建立了my_param1参数与my_val变量之间的关联。因此，每当参数值发生变化时，my_val的值将自动更新以反映新的参数值。

## 设置参数

类似地，参数模块提供了以下宏来轻松设置参数值。然而，由于使用了查询方法来获取参数，因此效率可能相对较低。

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

更高效的方法是使用`PARAM_GET()`宏找到相应的参数，然后利用`param_set_val`函数修改其值。采用这种方法可以消除在修改参数时重复查询的需要，从而提高效率。
```
param_t* param = PARAM_GET(New_Group, my_param1);
param_set_val(param, &((float) { 3.5 }));
```


> 请注意，在修改参数值后，您必须调用`param_save(path)`来保存更新后的参数。这一步确保修改后的数值被持久化保存，以便将来使用。`

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
