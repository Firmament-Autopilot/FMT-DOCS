
## 部署模型

将模型生成的代码部署到 FMT Firmware 中非常简单。基本上，你只需要将生成的文件（*.c,*.h）复制到相应模块的位置并替换旧代码即可。以下是存放模型代码的路径：

- **FMS**: `FMT-Firmware/src/model/fms/base_fms/lib`
- **INS**: `FMT-Firmware/src/model/ins/base_ins/lib`
- **Controller**: `FMT-Firmware/src/model/control/base_controller/lib`
- **Plant**: `FMT-Firmware/src/model/plant/multicopter/lib`

如果没有你新建模型对应的模块目录，请按照以下步骤创建一个（以控制器为例）：

- 复制 `FMT-Firmware/src/model/control/template_controller` 目录并进行重命名，比如 `FMT-Firmware/src/model/control/fw_controller`.
- 将所有生成的代码（*.c, *.h） 复制到 `FMT-Firmware/src/model/control/fw_controller/lib` 目录下.
- 修改 `control_interface.c`。添加模型接口代码来跟其它模块建立连接。

## 部署 C/C++ 代码

如果你已有C/C++写的算法并想合入到 FMT Firmware 中，也是可以的。一般来说，整个过程与模型部署类似。不同点是不是复制生成的代码，而是将 C/C++ 代码复制到 `FMT-Firmware/src/model/control/fw_controller/lib` 文件夹并保留 *Controller.h* 文件。
