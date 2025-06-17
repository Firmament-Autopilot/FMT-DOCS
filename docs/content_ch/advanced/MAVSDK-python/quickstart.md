## MAVSDK-Python介绍
MAVSDK-Python 是一个用于控制 MAVLink 无人机（如 PX4）的现代化 Python 开发工具包，采用异步编程模型，接口简洁易用，支持快速实现自动起降、航点飞行、状态监控等功能，广泛应用于无人机开发、测试与任务自动化场景。

## 环境配置
- Linux

我们以在ubuntu20.04下安装使用MAVSDK-Python为例。

1.安装mavsdk-python库
```
pip3 install mavsdk==2.8.4
```
2.克隆MAVSDK-Python(2.8.4)
```
git clone --branch 2.8.4 --recursive https://github.com/mavlink/MAVSDK-Python.git
```
3.安装ipython
```
sudo apt install python3-pip
pip3 install ipython
```

## 运行mavsdk-python
接下来以使用FMT官方飞控S1为例，演示如何运行mavsdk-python官方工程example中的takeoff_and_land.py示例以及使用ipython控制无人机起飞降落。

在开始之前编译并烧录四旋翼的纯硬件仿真(SIH)固件
```
cd .\target\sieon\s1\
scons -j4 --sim=SIH
python.exe ./uploader.py
```
烧录完固件后连接飞控至地面站，并使用TTL转USB线连接上位机以及飞控的```TELEM2```端口（具体操作请参考[MAVSDK-C++/连接飞控](content_ch/advanced/MAVSDK-C++/quickstart.md#连接飞控)）

在Ubuntu20.04终端中运行一个mavsdk_server用于保持和飞控的稳定连接(全程保留，所有示例程序默认使用这个mavsdk_server)。
```
mavsdk_server -p 50051 --system-address serial:///dev/ttyUSB0:115200
```
### 运行takeoff_and_land.py
在VScode中打开下载的MAVSDK-Python工程,修改examples/takeoff_and_land.py文件内容(可以自建python文件，然后运行)。
```
#!/usr/bin/env python3

import asyncio
from mavsdk import System


async def run():

    drone = System(mavsdk_server_address="localhost", port=50051)
    await drone.connect()

    print("Waiting for drone to connect...")
    async for state in drone.core.connection_state():
        if state.is_connected:
            print(f"-- Connected to drone!")
            break

    print("Waiting for drone to have a global position estimate...")
    async for health in drone.telemetry.health():
        if health.is_global_position_ok and health.is_home_position_ok:
            print("-- Global position estimate OK")
            break

    print("-- Arming")
    await drone.action.arm()

    print("-- Taking off")
    await drone.action.takeoff()

    await asyncio.sleep(10)

    print("-- Landing")
    await drone.action.land()



if __name__ == "__main__":
    # Run the asyncio loop
    asyncio.run(run())

```
进入 MAVSDK-Python 目录后，通过以下命令运行 takeoff_and_land.py 脚本：
```
examples/takeoff_and_land.py 
```
### ipython
开启一个新终端，输入`ipython`，输入`from mavsdk import System`
```
ajex@ubuntu:~$ ipython
Python 3.8.10 (default, Mar 18 2025, 20:04:55) 
Type 'copyright', 'credits' or 'license' for more information
IPython 8.12.3 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from mavsdk import System
```
然后创建一个 System 对象（在此例中命名为 drone,指定连接至本地运行的mavsdk_server），并使其连接到无人机（这个对象是我们的“句柄”，用于访问 MAVSDK 的其余功能）。
```
In [2]: drone = System(mavsdk_server_address="localhost", port=50051)

In [3]: await drone.connect()
```
连接成功后，我们就可以使用相应的 MAVSDK 命令进行解锁（arm）,起飞（takeoff）以及降落（land）。
```
In [5]: await drone.action.arm()

In [6]: await drone.action.takeoff()

In [7]: await drone.action.land()
```