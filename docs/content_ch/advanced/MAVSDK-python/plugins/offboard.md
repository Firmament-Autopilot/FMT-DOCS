## API
| API 函数                                    | 说明                               | 示例参数类型                                                    |
| ----------------------------------------- | -------------------------------- | --------------------------------------------------------- |
| `set_position_ned(PositionNedYaw)`        | 设置无人机以本地 NED 坐标系（北、东、下）的位置和朝向。   | `PositionNedYaw(x, y, z, yaw)`                            |
| `set_velocity_ned(VelocityNedYaw)`        | 设置无人机以本地 NED 坐标系的速度和朝向角。         | `VelocityNedYaw(vx, vy, vz, yaw)`                         |
| `set_velocity_body(VelocityBodyYawspeed)` | 设置无人机相对于机体坐标系的速度和偏航速率。           | `VelocityBodyYawspeed(vx, vy, vz, yawspeed)`              |
| `set_position_global(PositionGlobalYaw)`  | 设置无人机全局GPS坐标系的目标位置（经度、纬度、高度）和朝向。 | `PositionGlobalYaw(lat, lon, alt, yaw, altitude_type)`    |
| `set_attitude(Attitude)`                  | 设置无人机的姿态角（俯仰、滚转、偏航）和油门（0\~1）。    | `Attitude(pitch_deg, roll_deg, yaw_deg, throttle)`        |
| `set_attitude_rate(AttitudeRate)`         | 设置无人机的姿态角速度（俯仰速率、滚转速率、偏航速率）和油门。  | `AttitudeRate(pitch_rate, roll_rate, yaw_rate, throttle)` |
| `set_acceleration_ned(AccelerationNed)`   | 设置无人机以NED坐标系的加速度（m/s²）。          | `AccelerationNed(ax, ay, az)`                             |
| `start()`                                 | 开始 Offboard 控制模式。                | 无                                                         |
| `stop()`                                  | 停止 Offboard 控制模式。                | 无                                                         |


## 示例程序
这段示例程序演示了使用 MAVSDK-Python 的 Offboard 插件，按顺序连接飞行器、启动 Offboard 模式、解锁飞行器，然后依次调用设置位置（NED 和全球坐标系）、速度（NED 和机体系）、加速度、姿态和姿态速率的接口，最后停止 Offboard 控制。

在终端中运行一个mavsdk_server用于保持和飞控的稳定连接。
```
mavsdk_server -p 50051 --system-address serial:///dev/ttyUSB0:115200
```

新开一个终端进入MAVSDK-Python目录通过命令行启动示例程序
```
examples/offboard_api_demo.py
```
在`examples`目录下新建文件offboard_api_demo.py文件内容为
```
#!/usr/bin/env python3
import asyncio
from mavsdk import System
from mavsdk.offboard import PositionGlobalYaw
from mavsdk.offboard import (
    OffboardError,
    PositionNedYaw,
    VelocityNedYaw,
    VelocityBodyYawspeed,
    PositionGlobalYaw,
    Attitude,
    AttitudeRate,
    AccelerationNed
)
async def run():
    drone = System(mavsdk_server_address="localhost", port=50051)
    await drone.connect()

    print("Waiting for drone to connect...")
    async for state in drone.core.connection_state():
        if state.is_connected:
            print("-- Connected")
            break

    print("Waiting for global position estimate...")
    async for health in drone.telemetry.health():
        if health.is_global_position_ok and health.is_home_position_ok:
            print("-- Position estimate OK")
            break

    print("set position update rate")
    await drone.telemetry.set_rate_position(1.0)

    print("-- Sending initial setpoint")
    await drone.offboard.set_velocity_ned(VelocityNedYaw(0.0, 0.0, 0.0, 0.0))

    print("-- Starting Offboard mode")
    try:
        await drone.offboard.start()
    except OffboardError as error:
        print(f"Offboard start failed: {error._result.result}")
        return
    
    print("-- Arming")
    await drone.action.arm()

    print("-- Set Position NED")
    await drone.offboard.set_position_ned(PositionNedYaw(10.0, 100.0, -2.0, 0.0))
    await asyncio.sleep(5)

    print("-- Set Velocity NED")
    await drone.offboard.set_velocity_ned(VelocityNedYaw(1.0, 0.0, 0.0, 90.0))
    await asyncio.sleep(5)

    print("-- Set Velocity Body")
    await drone.offboard.set_velocity_body(VelocityBodyYawspeed(1.0, 0.0, 0.0, 30.0))
    await asyncio.sleep(5)

    print("-- Set Position Global (home position offset)")
    # 获取当前的位置作为目标位置
    async for telemetry_data in drone.telemetry.position():
        await drone.offboard.set_position_global(
            PositionGlobalYaw(
                telemetry_data.latitude_deg,
                telemetry_data.longitude_deg,
                telemetry_data.relative_altitude_m + 5.0,  # 上升 5 米
                180.0,  # 朝向南
                PositionGlobalYaw.AltitudeType.REL_HOME
            )
        )
        break  # 拿到一帧数据就退出循环
    await asyncio.sleep(5)

    print("-- Set Attitude (pitch 10 deg)")
    await drone.offboard.set_attitude(Attitude(-30.0, 0.0, 0.0, 0.6))
    await asyncio.sleep(3)

    print("-- Set Attitude Rate (yaw 30 deg/s)")
    await drone.offboard.set_attitude_rate(AttitudeRate(0.0, 0.0, 30.0, 0.5))
    await asyncio.sleep(3)

    print("-- Set Acceleration NED")
    await drone.offboard.set_acceleration_ned(AccelerationNed(0.5, 0.0, 0.0))
    await asyncio.sleep(3)
    
    print("-- Stopping Offboard")
    try:
        await drone.offboard.stop()
    except OffboardError as error:
        print(f"Offboard stop failed: {error._result.result}")
    # print("-- Landing")
    # await drone.action.land()
if __name__ == "__main__":
    asyncio.run(run())
```