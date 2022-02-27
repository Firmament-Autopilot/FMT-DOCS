
# uMCN

## 介绍

uMCN (Micro Multi-Communication Node) 提供了一种基于发布/订阅模式的安全跨线程/进程的通信方式。在系统中，uMCN 被广泛应用于任务和模块间的数据通信。

## 接口函数

```c
fmt_err_t mcn_init(void);
fmt_err_t mcn_advertise(McnHub_t hub, int (*echo)(void* parameter));
McnNode_t mcn_subscribe(McnHub_t hub, MCN_EVENT_HANDLE event, void (*pub_cb)(void* parameter));
fmt_err_t mcn_unsubscribe(McnHub_t hub, McnNode_t node);
fmt_err_t mcn_publish(McnHub_t hub, const void* data);
bool mcn_poll(McnNode_t node_t);
bool mcn_wait(McnNode_t node_t, int32_t timeout);
fmt_err_t mcn_copy(McnHub_t hub, McnNode_t node_t, void* buffer);
fmt_err_t mcn_copy_from_hub(McnHub_t hub, void* buffer);
void mcn_suspend(McnHub_t hub);
void mcn_resume(McnHub_t hub);
McnList_t mcn_get_list(void);
McnHub_t mcn_iterate(McnList_t* ite);
void mcn_node_clear(McnNode_t node_t);
```

## 添加新主题

为了添加新的主题 (topic)，你需要先创建一个主题内容。例如：

```c
typedef struct {
	uint32_t a;
	float b;
	int8_t c[4];
} data_content;
```

uMCN对主题内容的长度和类型没有限制，所以理论上可以用来传输任何类型的消息。

然后你需要使用宏 `MCN_DEFINE(name, size)` 来定义主题。一般在发布主题的源文件的顶部定义主题。例如：

```c
MCN_DEFINE(my_topic, sizeof(data_content));
```

uMCN 支持一个主题拥有多个发布者和订阅者。注意同一个主题名字不同被重复定义，不然编译器会报错。

下一步就是使用 `mcn_advertise()` 来注册主题。例如：

```c
mcn_advertise(MCN_ID(my_topic), my_topic_echo);
```

`MCN_ID()` 宏根据主题名获得枢纽节点。`my_topic_echo` 是一个回调函数，用来打印主题的数据。

```c
static int my_topic_echo(void* param)
{
	data_content data;
	if(mcn_copy_from_hub((McnHub*)param, &data) == FMT_EOK){
		printf("a:%d b:%f c:%c %c %c %c\n", data.a, data.b, data.c[0], data.c[1], data.c[2], data.c[3]);
        return 0;
	}
	return -1;
}
```

## 发布主题

可以在系统的任意位置使用函数 `mcn_publish()` 来发布一个主题。例如：

```c
data_content my_data = {50, -2.0, {1，2，3，4}}；
mcn_publish(MCN_ID(my_topic), &my_data);
```

## 订阅主题

uMCN 支持使用同步或异步方法订阅主题。对于同步方法，订阅主题时需要提供事件处理句柄。下面是一个例子：

**同步订阅**

```c
rt_sem_t event = rt_sem_create("my_event", 0, RT_IPC_FLAG_FIFO);
McnNode_t my_nod = mcn_subscribe(MCN_ID(my_topic), event, NULL);
```

**异步订阅**

```c
McnNode_t my_nod = mcn_subscribe(MCN_ID(my_topic), NULL, NULL);
```

> 注意，如果你在定义主题之外的文件来访问该主题，你需要使用宏 `MCN_DECLARE(name)` 来进行申明。

相应地，有同步和异步读取主题的方法。

**同步读取**

```c
data_content read_data;
if(mcn_wait(my_nod, RT_WAIT_FOREVER)){
	mcn_copy(MCN_ID(my_topic), my_nod, &read_data);
}
```

**异步读取**

```c
data_content read_data;
if(mcn_poll(my_nod){
	mcn_copy(MCN_ID(my_topic), my_nod, &read_data);
}
```

## 命令

```
usage: mcn <command> [options]

command:
 list        List all uMCN topics.
 echo        Echo a uMCN topic.
 suspend     Suspend a uMCN topic.
 resume      Resume a uMCN topic.
```