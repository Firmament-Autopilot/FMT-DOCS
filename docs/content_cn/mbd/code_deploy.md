
#### 部署模型

将模型生成的代码部署到 FMT Firmware 中，你只需要将模型生成的`*.c/*.h`文件复制到`FMT-Firmware/src/model`目录下对应模块的位置即可。以下是存放模型代码的路径：



例如你修改多旋翼的Controller模型，将生成的代码替换到如下目录

- **Controller**: `FMT-Firmware/src/model/control/mc_controller/lib`



#### 新建部署模型

如果你的模型是新创建的，没有对应的模型目录，可以按照以下步骤创建一个，这里还是以Controller为例：

- 复制 `FMT-Firmware/src/model/control/template_controller` 目录并进行重命名，比如 `FMT-Firmware/src/model/control/my_controller`.
- 将所有生成的代码`*.c, *.h`复制到 `FMT-Firmware/src/model/control/my_controller/lib` 目录下.
- 对该目录下的 `control_interface.c` 模型接口文件进行必要的修改。包括模型参数以及日志数据的修改等，以及进行其他必要的调整，以确保模型正确集成。

#### 模型接口文件

模型接口文件是一个C文件，位于模型所在的目录下，用于在嵌入式系统和模型文件之间建立连接。

模型接口的职责包括：

- 调用模型的 `Init()` 和 `Step()` 函数。
- 订阅模型的输入数据，并发布模型的输出数据。
- 定义模型参数并将其绑定到模型的参数。
- 定义模型日志数据，用于数据仿真或记录目的。

<p align="left">
 <img src="content_cn/mbd/figures/model_interface.png" width="30%">
</p>


#### 编译模型

要编译新加入的模型，可以修改位于BSP的config目录中的`model.py` 文件，该文件用于配置哪些模型需要进行编译。

如下所示，若要使用新添加的`my_controller`替换默认的`mc_controller`，则将配置文件中的`control/mc_controller`替换为`control/my_controller`，然后重新编译即可。

```
# Modify this file to decide which model are compiled
from building import *

vehicle_type = GetOption('vehicle')
sim_mode = GetOption('sim')

if vehicle_type == 'Multicopter':
    if sim_mode == 'HIL':
        MODELS = [
            'plant/template_plant',
            'ins/external_ins',
            'fms/mc_fms',
            'control/mc_controller',	# change this for HIL simulation
        ]
    else:
        MODELS = [
            'plant/multicopter',
            'ins/cf_ins',
            'fms/mc_fms',
            'control/mc_controller',	# change this for real flight
        ]
......
```
> 这里的vehicle_type对应在编译固件时候的`--vehicle`参数，默认是Multicopter。如果希望修改其它载具类型的模型，如Fixwing，则需要修改`vehicle_type == Fixwing`下的模型路径

#### 部署 C/C++ 代码

如果您有一个用 C/C++ 编写的算法想要集成到 FMT-Firmware 中，是完全可能的。整个过程类似于部署一个模型，即将你待部署的算法文件`*.c/*.h`放置在对应模型的lib目录，然后修改模型接口文件。

更详细的信息和指导可以参考 `FMT-Firmware/src/model/ins/px4_ecl` 目录。该目录提供了如何将自定义算法集成到 FMT-Firmware 中的见解。

通过查看 `FMT-Firmware/src/model/ins/px4_ecl` 目录的内容和结构，您可以深入了解集成自己的 C/C++ 算法所涉及的步骤。
