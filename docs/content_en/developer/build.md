## Build System

FMT build system is based on SCons.  SCons is an open source build system written in the Python language, similar to GNU Make. It uses a different approach than the usual Makefile, but instead uses SConstruct and SConscript files instead. These files are also Python scripts that can be written using standard Python syntax, so the Python standard library can be called in SConstruct, SConscript files for a variety of complex processing, not limited to the rules set by the Makefile.

A detailed [SCons user manual](http://www.scons.org/doc/production/HTML/scons-user/index.html) can be found on the SCons website. 

### What is Construction Tool

A software construction tool is a piece of software that compiles source code into an executable binary program according to certain rules or instructions. This is the most basic and important feature of building tools. In fact, these are not the only functions of construction tools. Usually these rules have a certain syntax and are organized into files. These files are used to control the behavior of the build tool, and you can do other things besides software building.

The most popular build tool today is GNU Make. Many well-known open source software, such as the Linux kernel, are built using Make. Make detects the organization and dependencies of the file by reading the Makefile and completes the commands specified in the Makefile.

Due to historical reasons, the syntax of the Makefile is confusing, which is not conducive to beginners. In addition, it is not convenient to use Make on the Windows platform, you need to install the Cygwin environment. To overcome the shortcomings of Make, other build tools have been developed, such as CMake and SCons.

### Commonly Used SCons Commands

This section describes the SCons commands that are commonly used in FMT.

#### scons

Go to the BSP directory to be compiled, and then use this command to compile the project directly. If some source files are modified after executing the `scons` command, and the scons command is executed again, SCons will incrementally compile and compile only the modified source files and link them.

`scons` can also be followed by a `-s` parameter, the command `scons -s`, which differs from the `scons` command in that it does not print specific internal commands.

#### scons -c

Clear the compilation target. This command clears the temporary and target files generated when `scons` is executed.

#### scons -jN

Multi-threaded compilation target, you can use this command to speed up compilation on multi-core computers. In general, a cpu core can support 2 threads. Use the `scons -j4` command on a dual-core machine.

> If you just want to look at compilation errors or warnings, it's best not to use the -j parameter so that the error message won't be mixed with multiple files in parallel.

#### scons --vehicle=XXX

Specify the vehicle type. Such as `scons --vehicle=Multicopter` or `scons --vehicle=Fixwing`. The vehicle type can be accessed by build config file such as `model.py`, with specified vehicle the different models will be compiled.

Specify the desired vehicle type during compilation, such as `scons --vehicle=Multicopter` or `scons --vehicle=Fixwing`. You can access the vehicle type through the build configuration file, like `model.py`. By selecting a specific vehicle type, the related models will be compiled.

#### scons --airframe=XXXX

Specify the airframe type. Such as `scons --airframe=1`. The controller reads this airframe type and chooses the appropriate controller mixer for the specified airframe.

#### scons --verbose

Print verbose information during build.

### SCons Advanced

SCons uses SConscript and SConstruct files to organize the source structure. Usually a project has only one SConstruct, but there will be multiple SConscripts. In general, an SConscript will be placed in each subdirectory where the source code is stored.

RT-Thread provides a separate file for each BSP called `rtconfig.py`, which defines the compiler options and board information. So the following three files exist in every FMT BSP directory: `rtconfig.py`, `SConstruct`, and `SConscript`, which control the compilation of the BSP. There is only one SConstruct file in a BSP, but there are multiple SConscript files. It can be said that the SConscript file is the main force of the organization source code.

To better support system customization, FMT provides configuration files for compiling modules. In each BSP, you'll find a `config`directory housing numerous Python files responsible for defining the modules to be compiled.

For example, you can specify a driver that be compiled by making changes to the **driver.py** file.

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

And you are able to select the model employed for a particular vehicle type by making adjustments to the **model.py** file.

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

### SCons Build-In Functions

If you want to add some of your own source code to the SCons build environment, you can usually create or modify an existing SConscript file. The SConscript file can control the addition of source files and can specify the group of files.

SCons provides a lot of built-in functions to help us quickly add source code, and with these simple Python statements we can add or remove source code to our project. The following is a brief introduction to some common functions.

#### GetCurrentDir()

Get current directory.

#### Glob('*.c')

Get all C files in the current directory. Modify the value of the parameter to match the suffix to match all files of the current directory.

#### GetDepend(macro)

This function is defined in the script file in the `tools` directory. It reads the configuration information from the rtconfig.h file with the macro name in rtconfig.h. This method (function) returns true if rtconfig.h has a macro turned on, otherwise it returns false.

#### Split(str)

Split the string str into a list list.

#### DefineGroup(name， src， depend，**parameters)

This is a method (function) of RT-Thread based on the SCons extension. DefineGroup is used to define a component. A component can be a directory (under a file or subdirectory) and a Group or folder in some subsequent IDE project files.

Parameter description of `DefineGroup()` :

| **Parameter** | **D**escription                                              |
| :------------ | :----------------------------------------------------------- |
| name          | name of Group                                                |
| src           | The files contained in the Group generally refer to C/C++ source files. For convenience, you can also use the Glob function to list the matching files in the directory where the SConscript file is located by using a wildcard. |
| depend        | The options that the Group depends on when compiling. The compile option generally refers to the FMT_USING_xxx macro defined in fmtconfig.h. When the corresponding macro is defined in the fmtconfig.h configuration file, then this group will be added to the build environment for compilation. If the dependent macro is not defined in fmtconfig.h, then this Group will not be added to compile. |
| parameters    | Configure other parameters, the values can be found in the table below. You do not need to configure all parameters in actual use. |

parameters that could be added：

| **P**arameter | **Description**                                              |
| :------------ | :----------------------------------------------------------- |
| CCFLAGS       | source file compilation parameters                           |
| CPPPATH       | head file path                                               |
| CPPDEFINES    | Link parameter                                               |
| LIBRARY       | Include this parameter, the object file generated by the component will be packaged into a library file |

#### SConscript(dirs，variant_dir，duplicate)

Read the new SConscript file, and the parameter description of the SConscript() function is as follows:

| **Parameter** | **Description**                                            |
| :------------ | :--------------------------------------------------------- |
| dirs          | SConscript file path                                       |
| variant_dir   | Specify the path to store the generated target file        |
| duiplicate    | Set whether to copy or link the source file to variant_dir |

## SConscript Examples

Below we will use a few SConscript as an example to explain how to use the scons tool.

### SConscript Example 1

Let's start with the SConcript file in the ICF5 BSP directory. This file manages all the other SConscript files under the BSP, as shown below.

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

- `import os:` Importing the Python system programming os module, you can call the functions provided by the os module to process files and directories.
- `cwd = GetCurrentDir()：` Get the current path and save it to the string variable cwd.
- `objs = []:` An empty list variable objs is defined.
- `list = os.listdir(cwd)：` Get all the subdirectories under the current directory and save them to the variable list.

- This is followed by a python for loop that walks through all the subdirectories of the BSP and runs the SConscript files for those subdirectories. The specific operation is to take a subdirectory of the current directory, use `os.path.join(cwd，d)` to splicing into a complete path, and then determine whether there is a file named SConscript in this subdirectory. If it exists, execute `objs = objs + SConscript(os.path.join(d，'SConscript'))` . This sentence uses a built-in function `SConscript()` provided by SCons, which can read in a new SConscript file and add the source code specified in the SConscript file to the source compilation list objs.

With this SConscript file, the source code required by the BSP project is added to the compilation list.

### SConscript Example 2

So what about ICF5 BSP other SConcript files? Let's take a look at the SConcript file in the drivers directory, which will manage the source code under the drivers directory. The drivers directory is used to store the underlying driver code implemented by the driver framework provided by FMT.

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

- `from building import *：` All the contents of the building module are imported into the current module, and the DefineGroup used later is defined in this module.
- `cwd = GetCurrentDir()：` Get the current path and save it to the string variable cwd.
- `Glob('*.c'):`Get all C files in the current directory. 

The last line uses DefineGroup to create a group called PeripheralDriver, which corresponds to the grouping in the IDE. The source code file for this group is the file specified by src, and the dependency is empty to indicate that the group does not depend on any macros of fmtconfig.h.

### SConscript Example 3

Next, let's take a look at an example of an FMT module SConscript file, which load the module build list from `module.py`.

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

The `bl.MODULES` definition can be found within the module.py file located in the BSP config folder. This definition specifies the source and header files that require compilation. It offers a user-friendly approach for tailoring the system.

### Compiler Options

`rtconfig.py` is a RT-Thread standard compiler configuration file that controls most of the compilation options. It is a script file written in Python and is used to do the following:

- Specify the compiler (choose one of the multiple compilers you support).
- Specify compiler parameters such as compile options, link options, and more.

When we compile the project using the scons command, the project is compiled according to the compiler configuration options of `rtconfig.py`. The following code is part of the rtconfig.py code in the ICF5 BSP directory.

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

Where CFLAGS is the compile option for C files, AFLAGS is the compile option for assembly files, and LFLAGS is the link option. The BUILD variable controls the level of code optimization. The default BUILD variable takes the value `debug`, which is compiled using debug mode, with an optimization level of 0. If you change BUILD variable to the value `release`, it will compile with optimization level 2.

```
BUILD = 'debug'
BUILD = 'release'
```

