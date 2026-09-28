## Task

In FMT, the task acts as an application operating within an independent thread, which is responsible for executing specific tasks. The FMT system includes a task manager that effectively oversees task behavior, such as initialization, starting, stopping, and more. Additionally, users have the convenience of effortlessly adding their custom tasks to enhance the system's overall functionality.

### Create Task

Tasks within the FMT-Firmware can be situated anywhere, but typically, there are two designated locations to accommodate them. 

The first location is in `FMT-Firmware/src/task`, where common tasks for all BSP (Board Support Package) are stored. These tasks should be hardware-independent, providing functionality that remains consistent across the entire system.

The second location is within the BSP folder, for instance, `FMT-Firmware/target/amov/icf5/tasks`. Here, custom tasks can be created, tailored specifically for the hardware in use, allowing for the implementation of hardware-specific functionalities.

To illustrate the task creation process, let's consider an example where we create a task within a BSP folder:

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

The `__fmt_task_desc` serves as the task description, essential for task definition within the system. By using this description, the task manager becomes aware of the task's existence and can add it to the task manager's list.

This task description defines the fundamental configuration and attributes of the task:

- `name`: Name of the task.
- `init`: Function responsible for task initialization.
- `entry`: Function representing the main entry point of the task.
- `priority`: Task priority, where a lower number indicates higher priority.
- `auto_start`: If set to true, the task will be automatically started by the task manager.
- `stack_size`: The size of the task's stack.
- `param`: Task parameter or additional data required for the task.
- `dependency`: Task dependency, which allows a task to rely on another task. This ensures that the initialization of the current task will only commence after the initialization of the dependent task has been completed. The following is an example:

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

Upon system startup, all tasks will be displayed on the system banner, along with their respective initialization statuses.

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

### Start Task

When `auto_start` is set to true, the task will be initiated and started automatically by the task manager. 

However, if `auto_start` is false, it becomes the user's responsibility to manually start the task using the `task start <task_name>` command. For instance, we can start a local task by executing the following command.

```
msh />task start local
msh />Hello FMT!
This is a local demo task!
This is a local demo task!
This is a local demo task!
This is a local demo task!
```



