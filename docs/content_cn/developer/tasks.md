## 任务

在FMT中，任务（Task）充当在独立线程内运行的应用程序，负责执行特定的任务。FMT系统包括任务管理器，有效地监控任务的行为，例如初始化、启动、停止等。此外，用户可以轻松地添加他们的自定义任务，以增强系统的整体功能性。

### 创建任务

在FMT-Firmware中，任务可以放置在任何位置，但通常有两个指定的位置来容纳它们。

第一个位置是在`FMT-Firmware/src/task`，这里存放着所有BSP（板级支持包）通用的任务。这些任务应该是硬件无关的，提供在整个系统中保持一致的功能。

第二个位置是在特定的BSP文件夹中，例如`FMT-Firmware/target/amov/icf5/tasks`。在这里，可以创建定制的任务，专门针对正在使用的硬件，允许实现硬件特定的功能。

为了说明任务创建的过程，让我们考虑一个例子，在一个BSP文件夹内创建一个任务：

```c
// target\amov\icf5\tasks\local_task.c

#include <firmament.h>

#include "module/task_manager/task_manager.h"

fmt_err_t task_local_init(void)
{
    return FMT_EOK;
}

void task_local_entry(void* parameter)
{
    printf("Hello FMT!\n");

    /* main loop */
    while (1) {
        printf("This is a local demo task!\n");
        sys_msleep(1000);
    }
}

TASK_EXPORT __fmt_task_desc = {
    .name       = "local",
    .init       = task_local_init,
    .entry      = task_local_entry,
    .priority   = 25,
    .auto_start = false,
    .stack_size = 1024,
    .param      = NULL,
    .dependency = NULL
};
```

`__fmt_task_desc`用作任务描述，在系统中定义任务时非常重要。通过使用这个描述，任务管理器可以知道任务的存在，并将其添加到任务管理器的列表中。

这个任务描述定义了任务的基本配置和属性：

- `name`: 任务的名称。
- `init`: 负责任务初始化的函数。
- `entry`: 任务的主入口函数。
- `priority`: 任务的优先级，较低的数字表示更高的优先级。
- `auto_start`: 如果设置为true，则任务将由任务管理器自动启动。
- `stack_size`: 任务的堆栈大小。
- `param`: 任务的参数或者任务所需的额外数据。
- `dependency`: 任务的依赖关系，允许一个任务依赖于另一个任务。这确保了当前任务的初始化只会在依赖任务初始化完成后开始。以下是一个示例：

```c
TASK_EXPORT __fmt_task2_desc = {
    .name = "mavobc",
    .init = task_mavobc_init,
    .entry = task_mavobc_entry,
    .priority = MAVOBC_THREAD_PRIORITY,
    .auto_start = true,
    .stack_size = 4096,
    .param = NULL,
    .dependency = (char*[]) { "mavgcs", NULL }
};
```

在系统启动时，所有任务及其各自的初始化状态将显示在系统横幅上。

```
   _____                               __ 
  / __(_)_____ _  ___ ___ _  ___ ___  / /_
 / _// / __/  ' \/ _ `/  ' \/ -_) _ \/ __/
/_/ /_/_/ /_/_/_/\_,_/_/_/_/\__/_//_/\__/ 
Firmware..................FMT FW v1.0.0-rc
Kernel....................RT-Thread v4.0.3
RAM.................................448 KB
Target...........................Amov-ICF5
Vehicle........................Multicopter
Airframe.................................1
INS Model..................Base INS v0.3.2
FMS Model..................Base FMS v0.4.0
Control Model.......Base Controller v0.2.4
Task Initialize:
  local.................................OK
  mavobc................................OK
  mavgcs................................OK
  logger................................OK
  status................................OK
  vehicle...............................OK
```

### 启动任务

当 `auto_start` 设置为 true 时，任务将由任务管理器自动初始化并启动。

然而，如果 `auto_start` 设置为 false，则用户需要手动使用 `task start <task_name>` 命令来启动任务。例如，我们可以通过执行以下命令来启动一个本地任务。

```
msh />task start local
msh />Hello FMT!
This is a local demo task!
This is a local demo task!
This is a local demo task!
This is a local demo task!
```



