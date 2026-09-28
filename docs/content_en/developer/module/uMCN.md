
# uMCN

## Introduction

The uMCN (Micro Multi-Communication Node) provides a secure inter-thread/inter-process communication method based on the *publish/subscribe* model. It is widely used in system to exchange data between tasks and modules.

## API

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

## Adding New Topic

To add a new uMCN topic, you need create a new topic content. e.g.

```c
typedef struct {
	uint32_t a;
	float b;
	int8_t c[4];
} data_content;
```

uMCN imposes no limitations on the length or type of topic content, making it theoretically capable of transmitting any message type.

To define a topic, you should utilize the macro `MCN_DEFINE(name, size)`. This macro is typically added at the beginning of the source file where the topic is published. For example, consider the following:

```c
MCN_DEFINE(my_topic, sizeof(data_content));
```

uMCN supports multiple publishers and subscribers for a single topic. However, it is essential to avoid using the same topic name more than once, as the compiler will generate an error in such cases.

The next step is to register this topic using `mcn_advertise()`. e.g.

```c
mcn_advertise(MCN_HUB(my_topic), my_topic_echo);
```

The `MCN_HUB()` macro is used to locate the hub node associated with a specific topic name. On the other hand, the `my_topic_echo` serves as the echo callback function responsible for displaying the topic data.

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

## Publish Topic

Publish a topic can be performed from any part of the system using the function `mcn_publish()`. For instance, consider the following:

```c
data_content my_data = {50, -2.0, {1，2，3，4}}；
mcn_publish(MCN_HUB(my_topic), &my_data);
```

## Subscribe Topic

uMCN provides support for subscribing to a topic using either synchronous or asynchronous methods. When opting for the synchronous approach, you will need to provide an event handle while subscribing to the topic. Here is an example to illustrate this:

**Synchronous subscription**

```c
rt_sem_t event = rt_sem_create("my_event", 0, RT_IPC_FLAG_FIFO);
McnNode_t my_nod = mcn_subscribe(MCN_HUB(my_topic), event, NULL);
```

**Asynchronous subscription**

```c
McnNode_t my_nod = mcn_subscribe(MCN_HUB(my_topic), NULL, NULL);
```

> Note that you need declare a topic with macro `MCN_DECLARE(name)` if you are visiting a topic outside of the source file where the topic defined.

Correspondingly, there are synchronous and asynchronous methods when reading a topic.

**Synchronous read**

```c
data_content read_data;
if(mcn_wait(my_nod, RT_WAIT_FOREVER)){
	mcn_copy(MCN_HUB(my_topic), my_nod, &read_data);
}
```

**Asynchronous read**

```c
data_content read_data;
if(mcn_poll(my_nod){
	mcn_copy(MCN_HUB(my_topic), my_nod, &read_data);
}
```

## Command

```
usage: mcn <command> [options]

command:
 list        List all uMCN topics.
 echo        Echo a uMCN topic.
 suspend     Suspend a uMCN topic.
 resume      Resume a uMCN topic.
```