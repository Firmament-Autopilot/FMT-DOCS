## API
API Name    | API Usage             | Input        
-----       | --------------        | -------------- 
arm()       | send arm command      | None     
disarm()    | send disarm command   | None       
takeoff()   | send takeoff command  | None 
land()      | send land command     | None 
reboot()    | send reboot command   | None
hold()      | send hold command     | None 
return_to_launch()      | send return to launch point command   | None
goto_location(latitude_deg, longitude_deg, absolute_altitude_m, yaw_deg)        | send go to given location command     | 期望达到的经度,纬度,高度以及运行过程中的偏航角度
set_takeoff_altitude(altitude)  | set the takeoff altitude              | 期望的起飞高度
set_current_speed(speed_m_s)     | set flight speed                      | 期望的巡航速度

> 根据FMT对```goto_location(latitude_deg, longitude_deg, absolute_altitude_m, yaw_deg))```函数的处理，其中的参数```yaw_deg```在移动到航点的过程中不会作为控制量用于控制被控对象在移动到指定的位置过程中的偏航。

## 测试程序
下面是一份用于测试action插件API的示例程序。程序发出的命令顺序为解锁、设置起飞高度、起飞、设置飞行速度、移动到指定位置、HOLD、返回起飞点、降落、上锁最后重启。

在终端中运行一个mavsdk_server用于保持和飞控的稳定连接。
```
mavsdk_server -p 50051 --system-address serial:///dev/ttyUSB0:115200
```

新开一个终端进入MAVSDK-Python目录通过命令行启动示例程序
```
examples/action_api_demo.py
```
在`examples`目录下新建文件action_api_demo.py文件内容为
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
    print("-- Setting takeoff altitude to 10 meters")
    await drone.action.set_takeoff_altitude(10.0)
    print("-- Taking off")
    await drone.action.takeoff()
    await asyncio.sleep(20)

    print("-- Set flight speed to 6 m/s")
    await drone.action.set_current_speed(6.0)
    await asyncio.sleep(5)


    print("-- Go to loaction (lat: 37.647042, lon: -122.456401, alt: 10.0)")
    await drone.action.goto_location(37.647042, -122.456401, 10.0, 0.0)
    await asyncio.sleep(10)
     
    print("-- Hold")
    await drone.action.hold()
    await asyncio.sleep(5)

    print("-- return to launch")
    await drone.action.return_to_launch()
    await asyncio.sleep(10)

    print("-- Landing") 
    await drone.action.land()
    await asyncio.sleep(5)

    print("-- Disarming")
    await drone.action.disarm()
    await asyncio.sleep(5)

    print("rebooting the drone")
    await drone.action.reboot()


if __name__ == "__main__":
    # Run the asyncio loop
    asyncio.run(run())

```