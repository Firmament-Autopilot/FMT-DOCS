
## 部署模型

将模型生成的代码部署到 FMT Firmware 中非常简单。基本上，你只需要将生成的文件（*.c,*.h）复制到相应模块的位置并替换旧代码即可。以下是存放模型代码的路径：

- **FMS**: `FMT-Firmware/src/model/fms/base_fms/lib`
- **INS**: `FMT-Firmware/src/model/ins/base_ins/lib`
- **Controller**: `FMT-Firmware/src/model/control/base_controller/lib`
- **Plant**: `FMT-Firmware/src/model/plant/multicopter/lib`

如果没有你新建模型对应的模块目录，请按照以下步骤创建一个（以控制器为例）：

- 复制 `FMT-Firmware/src/model/control/template_controller` 目录并进行重命名，比如 `FMT-Firmware/src/model/control/fw_controller`.
- 将所有生成的代码（*.c, *.h） 复制到 `FMT-Firmware/src/model/control/fw_controller/lib` 目录下.
- 打开 `control_interface.c` 文件并进行必要的修改。这包括根据您的模型需求添加参数和日志，以及进行其他必要的调整，以确保正确的集成。

## 模型接口

模型接口是一个C文件，用于在嵌入式系统和生成的模型文件之间建立连接。模型接口的职责包括：

- 调用模型的 `Init()` 和 `Step()` 函数。
- 订阅模型的输入数据，并发布模型的输出数据。
- 定义模型参数并将其绑定到模型的参数（可选）。
- 定义模型日志数据，用于数据仿真或记录目的（可选）。

<p align="center">
 <img src="figures/model_interface.png" width="45%">
</p>

## 部署 C/C++ 代码

如果您有一个用 C/C++ 编写的算法想要集成到 FMT-Firmware 中，是完全可能的。整个过程类似于部署一个模型。更详细的信息和指导可以参考 `FMT-Firmware/src/model/ins/px4_ecl` 目录。该目录提供了如何将自定义算法集成到 FMT-Firmware 中的见解。

通过查看 `FMT-Firmware/src/model/ins/px4_ecl` 目录的内容和结构，您可以深入了解集成自己的 C/C++ 算法所涉及的步骤。

## 编译你的模型

要构建您的模型，您可以修改位于板支持包（BSP）配置文件夹中的 model.py 文件。通过访问和修改这个文件，您可以根据特定的需求和偏好定制模型的构建过程。

以下是一个示例：

```
# Modify this file to decide which model are compiled
from building import *

vehicle_type = GetOption('vehicle')

if vehicle_type == 'Multicopter':
    MODELS = [
        'plant/multicopter',
        'ins/base_ins',
        'fms/base_fms',
        'control/base_controller',
    ]
elif vehicle_type == 'Fixwing':
    MODELS = [
        'plant/fixwing',
        'ins/base_ins',
        'fms/fw_fms',
        'control/fw_controller',
    ]
elif vehicle_type == 'Template':
    MODELS = [
        'plant/template_plant',
        'ins/template_ins',
        'fms/template_fms',
        'control/template_controller',
    ]
else:
    raise Exception("Wrong VEHICLE_TYPE %s defined" % vehicle_type)
```
当使用 `scons -j4 --vehicle=Multicopter` 调用构建过程时，将使用 MODELS 中定义的模型。如果您希望切换到另一个惯性导航系统（INS）模型，例如 EKF，您可以选择修改 `ins/base_ins` 的引用为 `ins/px4_ecl`。此外，您还保留了灵活性，可以引入新的 `vehicle_type` 并将您的定制模型构建到系统中。
