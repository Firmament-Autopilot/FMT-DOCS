### 构建系统

FMT的构建系统基于SCons。SCons是一种开源的构建系统，用Python语言编写，类似于GNU Make。它采用与通常的Makefile不同的方法，而是使用SConstruct和SConscript文件。这些文件也是Python脚本，可以使用标准的Python语法编写，在SConstruct和SConscript文件中可以调用Python标准库进行各种复杂的处理，不限于Makefile规则的设置。

你可以在[SCons官方用户手册](http://www.scons.org/doc/production/HTML/scons-user/index.html)上找到详细的说明。

### 构建工具

构建工具是一种将源代码根据一定的规则或指令编译成可执行的二进制程序的软件。这是构建工具最基本和重要的功能。事实上，构建工具不仅限于这些功能。通常，这些规则具有一定的语法，并且组织在文件中。这些文件用于控制构建工具的行为，除了软件构建之外还可以执行其他操作。

目前最流行的构建工具是GNU Make。许多著名的开源软件，如Linux内核，都是使用Make构建的。Make通过读取Makefile来检测文件的组织和依赖关系，并完成Makefile中指定的命令。

由于历史原因，Makefile的语法较为复杂，对初学者不利。此外，在Windows平台上使用Make不方便，需要安装Cygwin环境。为了克服Make的缺点，开发了其他构建工具，例如CMake和SCons。

### 常用的SCons命令

本节介绍在FMT中常用的SCons命令。

#### scons

进入要编译的BSP目录，然后使用这个命令直接编译项目。如果在执行 `scons` 命令后修改了一些源文件，并再次执行 `scons` 命令，SCons会增量编译，只编译修改过的源文件并链接它们。

`scons` 命令也可以跟随 `-s` 参数，即 `scons -s` 命令，与 `scons` 命令不同之处在于不会打印特定的内部命令。

#### scons -c

清除编译目标。这个命令会清除执行 `scons` 时生成的临时和目标文件。

#### scons -jN

多线程编译目标，可以在多核计算机上加快编译速度。通常一个CPU核心可以支持2个线程。在双核机器上可以使用 `scons -j4` 命令。

> 如果只想查看编译错误或警告，最好不要使用 `-j` 参数，这样错误消息不会与并行处理的多个文件混在一起。

#### scons --vehicle=XXX

指定车辆类型。例如 `scons --vehicle=Multicopter` 或 `scons --vehicle=Fixwing`。可以通过像 `model.py` 这样的构建配置文件访问车辆类型。通过选择特定的车辆类型，相关的模型将被编译。

#### scons --airframe=XXXX

指定机架类型。例如 `scons --airframe=1`。控制器读取这个机架类型，并为指定的机架选择适当的控制器混合器。

#### scons --verbose

在构建过程中打印详细信息。

### SCons高级用法

SCons使用SConscript和SConstruct文件组织源代码结构。通常一个项目只有一个SConstruct文件，但可能会有多个SConscript文件。一般来说，每个存储源代码的子目录中都会放置一个SConscript文件。

RT-Thread为每个BSP提供了一个名为 `rtconfig.py` 的单独文件，定义了编译器选项和板信息。因此，在每个FMT的BSP目录中存在以下三个文件：`rtconfig.py`、`SConstruct` 和 `SConscript`，它们控制BSP的编译。一个BSP只有一个SConstruct文件，但可能有多个SConscript文件。可以说SConscript文件是组织源代码的主要力量。

为了更好地支持系统定制，FMT提供了用于编译模块的配置文件。在每个BSP中，你会找到一个名为 `config` 的目录，其中包含多个Python文件，负责定义要编译的模块。

例如，你可以通过修改 `driver.py` 文件来指定要编译的驱动程序。

```python
# Modify this file to decide which drivers are compiled

DRIVERS = [
    'imu/bmi088.c',
    'imu/icm42688p.c',
    'imu/icm20948.c',
    'mag/bmm150.c',
    'barometer/spl06.c',
    'barometer/ms5611.c',
    'gps/gps_ubx.c',
    'rgb_led/aw2023.c',
    'mtd/w25qxx.c',
    'vision_flow/mtf_01.c',
    'airspeed/ms4525.c',
    'uwb/nlink_linktrack/*.c',
]

DRIVERS_CPPPATH = []
```

同时，你可以通过调整 **model.py** 文件来选择特定车辆类型所使用的模型。

```python
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

### SCons内置函数

如果你想将一些自己的源代码添加到SCons构建环境中，通常可以创建或修改现有的SConscript文件。SConscript文件可以控制添加源文件，并指定文件组。

SCons提供了许多内置函数来帮助我们快速添加源代码，通过这些简单的Python语句，我们可以向项目中添加或删除源代码。以下是一些常用函数的简要介绍。

#### GetCurrentDir()

获取当前目录。

#### Glob('*.c')

获取当前目录中的所有C文件。修改参数的值以匹配后缀以匹配当前目录中的所有文件。

#### GetDepend(macro)

此函数在`tools`目录中的脚本文件中定义。它通过宏名称从rtconfig.h文件中读取配置信息。如果rtconfig.h中有打开的宏，则此方法（函数）返回true，否则返回false。

#### Split(str)

将字符串 `str` 拆分成列表 `list`。

#### DefineGroup(name, src, depend, **parameters)

这是基于SCons扩展的RT-Thread方法（函数）。`DefineGroup` 用于定义一个组件。一个组件可以是一个目录（文件或子目录下）和一些后续IDE项目文件中的组或文件夹。

`DefineGroup()` 的参数描述：

| **Parameter** | **D**escription                                              |
| :------------ | :----------------------------------------------------------- |
| name          | name of Group                                                |
| src           | The files contained in the Group generally refer to C/C++ source files. For convenience, you can also use the Glob function to list the matching files in the directory where the SConscript file is located by using a wildcard. |
| depend        | The options that the Group depends on when compiling. The compile option generally refers to the FMT_USING_xxx macro defined in fmtconfig.h. When the corresponding macro is defined in the fmtconfig.h configuration file, then this group will be added to the build environment for compilation. If the dependent macro is not defined in fmtconfig.h, then this Group will not be added to compile. |
| parameters    | Configure other parameters, the values can be found in the table below. You do not need to configure all parameters in actual use. |

可以添加的参数：

| **P**arameter | **Description**                                              |
| :------------ | :----------------------------------------------------------- |
| CCFLAGS       | source file compilation parameters                           |
| CPPPATH       | head file path                                               |
| CPPDEFINES    | Link parameter                                               |
| LIBRARY       | Include this parameter, the object file generated by the component will be packaged into a library file |



#### SConscript(dirs, variant_dir, duplicate)
阅读新的 SConscript 文件，并且 SConscript() 函数的参数描述如下：

| **Parameter** | **Description**                                            |
| :------------ | :--------------------------------------------------------- |
| dirs          | SConscript file path                                       |
| variant_dir   | Specify the path to store the generated target file        |
| duiplicate    | Set whether to copy or link the source file to variant_dir |

## SConscript 示例
以下我们将使用几个 SConscript 作为示例，来解释如何使用 SCons 工具。


### SConscript 示例 1
让我们从位于 ICF5 BSP 目录下的 SConscript 文件开始。该文件管理 BSP 下的所有其他 SConscript 文件，如下所示。

```python
# for subdirectory compiling
import os
from building import *

cwd = GetCurrentDir()
objs = []
list = os.listdir(cwd)

for d in list:
    path = os.path.join(cwd, d)
    if os.path.isfile(os.path.join(path, 'SConscript')):
        objs = objs + SConscript(os.path.join(d, 'SConscript'))

Return('objs')
```

- `import os:` 导入 Python 系统编程的 os 模块，可以调用 os 模块提供的函数来处理文件和目录。
- `cwd = GetCurrentDir()：` 获取当前路径并保存到字符串变量 cwd 中。
- `objs = []:` 定义一个空列表变量 objs。
- `list = os.listdir(cwd)：` 获取当前目录下的所有子目录，并保存到变量 list 中。

- 接着是一个 Python 的 for 循环，遍历 BSP 的所有子目录，并运行这些子目录下的 SConscript 文件。具体操作是获取当前目录的一个子目录，使用 `os.path.join(cwd, d)` 拼接成完整路径，然后判断该子目录中是否存在名为 SConscript 的文件。如果存在，执行 `objs = objs + SConscript(os.path.join(d, 'SConscript'))`。这句话使用了 SCons 提供的内置函数 `SConscript()`，可以读取一个新的 SConscript 文件，并将该 SConscript 文件中指定的源代码添加到源代码编译列表 objs 中。

通过这个 SConscript 文件，BSP 项目所需的源代码被添加到了编译列表中。

### SConscript 示例 2

那么，ICF5 BSP 其他的 SConscript 文件呢？让我们看一下 drivers 目录中的 SConscript 文件，这个文件将管理 drivers 目录下的源代码。drivers 目录用于存储由 FMT 提供的驱动程序框架实现的底层驱动代码。

```python
# for driver compiling
import os
from building import *

cwd = GetCurrentDir()

src = Glob('*.c')
src += Glob('sdio/*.c')
src += Glob('usbd/*.c')

inc = [cwd]
inc += [cwd + '/sdio']
inc += [cwd + '/usbd']

group = DefineGroup('PeripheralDriver', src, depend = [''], CPPPATH = inc)

Return('group')
```
- `from building import *：` 将 building 模块的所有内容导入当前模块，在后续将使用该模块中定义的 DefineGroup。
- `cwd = GetCurrentDir()：` 获取当前路径并保存到字符串变量 cwd 中。
- `Glob('*.c'):` 获取当前目录中所有的 C 文件。

最后一行使用 DefineGroup 创建一个名为 PeripheralDriver 的组，该组对应于 IDE 中的分组。该组的源代码文件由 src 指定，依赖关系为空表示该组不依赖于 fmtconfig.h 的任何宏。

### SConscript 示例 3

接下来，让我们来看一个 FMT 模块的 SConscript 文件示例，该文件从 `module.py` 中加载模块构建列表。

```python
from building import *
import BuildLists as bl

cwd = GetCurrentDir()

src = []
for f in bl.MODULES:
    src += Glob(f)

inc = []
for d in bl.MODULES_CPPPATH:
    inc += [cwd + '/' + d]

group = DefineGroup('Module', src, depend=[''], CPPPATH=inc)

Return('group')
```

`bl.MODULES` 的定义可以在位于 BSP 配置文件夹中的 `module.py` 文件中找到。这个定义指定了需要编译的源文件和头文件，为定制系统提供了用户友好的方法。

### 编译器选项

`rtconfig.py` 是一个 RT-Thread 标准的编译器配置文件，控制大部分的编译选项。它是一个用 Python 编写的脚本文件，用于执行以下操作：

- 指定编译器（从支持的多个编译器中选择一个）。
- 指定编译器参数，如编译选项、链接选项等。

当我们使用 `scons` 命令编译项目时，项目会根据 `rtconfig.py` 的编译器配置选项进行编译。以下代码是位于 ICF5 BSP 目录中的 `rtconfig.py` 的一部分代码。

```python
import os

# board options
BOARD = 'amov-icf5'

# toolchains options
ARCH = 'arm'
CPU = 'cortex-m4'
CROSS_TOOL = 'gcc'
# build version: debug or release
BUILD = 'release'

if os.getenv('RTT_CC'):
    CROSS_TOOL = os.getenv('RTT_CC')

# cross_tool provides the cross compiler
# EXEC_PATH is the compiler execute path, for example, CodeSourcery, Keil MDK, IAR
if CROSS_TOOL == 'gcc':
    PLATFORM = 'gcc'
    EXEC_PATH = 'your-compiler-path'
else:
    print('================ERROR============================')
    print('Not support %s yet!' % CROSS_TOOL)
    print('=================================================')
    exit(0)

if os.getenv('RTT_EXEC_PATH'):
    EXEC_PATH = os.getenv('RTT_EXEC_PATH')

if PLATFORM == 'gcc':
    # toolchains
    PREFIX = 'arm-none-eabi-'
    CC = PREFIX + 'gcc'
    AS = PREFIX + 'gcc'
    AR = PREFIX + 'ar'
    CXX = PREFIX + 'g++'
    LINK = PREFIX + 'gcc'
    TARGET_EXT = 'elf'
    SIZE = PREFIX + 'size'
    OBJDUMP = PREFIX + 'objdump'
    OBJCPY = PREFIX + 'objcopy'

    DEVICE = '  -mcpu=cortex-m4 -mthumb -mfpu=fpv4-sp-d16 -mfloat-abi=hard -ffunction-sections -fdata-sections'
    CFLAGS = DEVICE + ' -g -Wall -Wstrict-aliasing=0 -Wno-uninitialized -Wno-unused-function -Wno-switch -DUSE_STDPERIPH_DRIVER -DGD32F470 -D__VFP_FP__ -DARM_MATH_CM4 -D__FPU_PRESENT="1" -D__FPU_USED="1"'
    AFLAGS = ' -c' + DEVICE + ' -x assembler-with-cpp -Wa,-mimplicit-it=thumb '
    LFLAGS = DEVICE + ' -lm -lgcc -lc' + \
        ' -nostartfiles -Wl,--gc-sections,--print-memory-usage,-Map=build/fmt_' + BOARD + '.map,-cref,-u,Reset_Handler -T link.lds'

    CPATH = ''
    LPATH = ''

    if BUILD == 'debug':
        CFLAGS += ' -O0 -gdwarf-2'
        AFLAGS += ' -gdwarf-2'
    else:
        CFLAGS += ' -O2'

    CXXFLAGS = CFLAGS
    CFLAGS += ' -std=c99'
    CXXFLAGS += ' -std=c++14'

    POST_ACTION = OBJCPY + ' -O binary $TARGET build/fmt_' + BOARD + '.bin\n' + SIZE + ' $TARGET \n'
```

在这里，CFLAGS 是用于 C 文件的编译选项，AFLAGS 是用于汇编文件的编译选项，而 LFLAGS 则是链接选项。BUILD 变量控制着代码的优化级别。默认情况下，BUILD 变量的取值是 `debug`，这意味着使用调试模式进行编译，优化级别为 0。如果将 BUILD 变量更改为 `release`，则会以优化级别 2 进行编译。

```
BUILD = 'debug'
BUILD = 'release'
```

