
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

uMCN has no restrictions on the length and type of topic content, so in theory, it can be used to transmit any message type.

Then you need add topic definition using the macro `MCN_DEFINE(name, size)`. Usually on the top of source file where the topic is published. e.g.

```c
MCN_DEFINE(my_topic, sizeof(data_content));
```

uMCN supports multiple publishers and subscribers for one toppic. Note that the same topic name is not allowed, as the compiler will complain about that.

The next step is to register this topic using `mcn_advertise()`. e.g.

```c
mcn_advertise(MCN_ID(my_topic), my_topic_echo);
```

`MCN_ID()` is a macro to find the hub node with a given topic name. `my_topic_echo` is the echo callback function which is used to print out the topic data.

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

Publishing a topic can be done from anywhere in the system using function `mcn_publish()`. e.g.

```c
data_content my_data = {50, -2.0, {1，2，3，4}}；
mcn_publish(MCN_ID(my_topic), &my_data);
```

## Subscribe Topic

The uMCN supports to subscribe a topic with either synchronous or asynchronous method. For synchronous method, you need provide an event handle when subscribing a topic. Here is an example:

**Synchronous subscription**

```c
rt_sem_t event = rt_sem_create("my_event", 0, RT_IPC_FLAG_FIFO);
McnNode_t my_nod = mcn_subscribe(MCN_ID(my_topic), event, NULL);
```

**Asynchronous subscription**

```c
McnNode_t my_nod = mcn_subscribe(MCN_ID(my_topic), NULL, NULL);
```

> Note that you need declare a topic with macro `MCN_DECLARE(name)` if you are visiting a topic outside of the source file where the topic defined.

Correspondingly, there are synchronous and asynchronous methods when reading a topic.

**Synchronous read**

```c
data_content read_data;
if(mcn_wait(my_nod, RT_WAIT_FOREVER)){
	mcn_copy(MCN_ID(my_topic), my_nod, &read_data);
}
```

**Asynchronous read**

```c
data_content read_data;
if(mcn_poll(my_nod){
	mcn_copy(MCN_ID(my_topic), my_nod, &read_data);
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