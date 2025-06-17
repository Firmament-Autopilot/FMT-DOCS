## API
| API Name                                | API Usage       | Input / Output Description                  |
| --------------------------------------- | --------------- | ------------------------------------------- |
| `set_rate_position(rate_hz)`            | 设置位置更新频率        | 更新频率（单位：Hz）                                 |
| `set_rate_altitude(rate_hz)`            | 设置高度更新频率        | 更新频率（单位：Hz）                                 |
| `set_rate_gps_info(rate_hz)`            | 设置 GPS 信息更新频率   | 更新频率（单位：Hz）                                 |
| `set_rate_in_air(rate_hz)`              | 设置是否在空中状态的更新频率  | 更新频率（单位：Hz）                                 |
| `set_rate_attitude_euler(rate_hz)`      | 设置欧拉角姿态更新频率     | 更新频率（单位：Hz）                                 |
| `set_rate_attitude_quaternion(rate_hz)` | 设置四元数姿态更新频率     | 更新频率（单位：Hz）                                 |
| `set_rate_velocity_ned(rate_hz)`        | 设置 NED 速度更新频率   | 更新频率（单位：Hz）                                 |
| `set_rate_home(rate_hz)`                | 设置 home 点信息更新频率 | 更新频率（单位：Hz）                                 |
| `set_rate_imu(rate_hz)`                 | 设置原始 IMU 数据更新频率 | 更新频率（单位：Hz）                                 |
| `set_rate_scaled_imu(rate_hz)`          | 设置缩放 IMU 数据更新频率 | 更新频率（单位：Hz）                                 |
| `telemetry.battery()`                   | 订阅电池状态          | 电量百分比 (`remaining_percent`)                 |
| `telemetry.gps_info()`                  | 订阅 GPS 信息       | Fix 类型、可见卫星数 (`fix_type`, `num_satellites`) |
| `telemetry.in_air()`                    | 订阅是否在飞行状态       | 布尔值                                         |
| `telemetry.position()`                  | 订阅位置信息          | 纬度，经度，绝对高度                                  |
| `telemetry.altitude()`                  | 订阅各种高度信息        | 单调高度、AMSL、本地、相对、地形、高度空隙                     |
| `telemetry.attitude_euler()`            | 订阅姿态（欧拉角）       | Roll, Pitch, Yaw（单位：度）                      |
| `telemetry.attitude_quaternion()`       | 订阅姿态（四元数）       | w, x, y, z                                  |
| `telemetry.velocity_ned()`              | 订阅 NED 速度       | 北、东、下方向速度                                   |
| `telemetry.home()`                      | 订阅 home 点位置信息   | 纬度，经度，绝对高度                                  |
| `telemetry.imu()`                       | 订阅高分辨率 IMU 数据   | 加速度、角速度、磁场、温度                               |
| `telemetry.scaled_imu()`                | 订阅缩放分辨率 IMU 数据  | 加速度、角速度、磁场、温度                               |

## 示例程序
下面是一份用于测试Telemetry插件API的示例程序,测试了所有支持的Telemetry消息的更新频率设置函数、获取函数以及订阅函数。

在运行程序之前，推荐通过地面站在SIH仿真下使无人机在Position模式下起飞并设定一个航点，或者也可以参照[action](content_ch/advanced/MAVSDK-python/plugins/action.md)中，通过action插件控制SIH仿真下的无人机移动，来观察Telemetry数据的更新。

在终端中运行一个mavsdk_server用于保持和飞控的稳定连接。
```
mavsdk_server -p 50051 --system-address serial:///dev/ttyUSB0:115200
```

新开一个终端进入MAVSDK-Python目录通过命令行启动示例程序
```
examples/telemetry_api_demo.py
```
在`examples`目录下新建文件telemetry_api_demo.py文件内容为
```
#!/usr/bin/env python3

import asyncio
from mavsdk import System


async def run():
    # Init the drone
    drone = System(mavsdk_server_address="localhost", port=50051)
    await drone.connect()

    # Set telemetry rates to 1 Hz
    await drone.telemetry.set_rate_position(1.0)
    await drone.telemetry.set_rate_altitude(1.0)
    await drone.telemetry.set_rate_gps_info(1.0)
    await drone.telemetry.set_rate_in_air(1.0)
    await drone.telemetry.set_rate_attitude_euler(1.0)
    await drone.telemetry.set_rate_attitude_quaternion(1.0)
    await drone.telemetry.set_rate_velocity_ned(1.0)
    await drone.telemetry.set_rate_home(1.0)
    await drone.telemetry.set_rate_imu(1.0)
    await drone.telemetry.set_rate_scaled_imu(1.0)

    # Start the telemetry print tasks
    asyncio.ensure_future(print_battery(drone))
    asyncio.ensure_future(print_gps_info(drone))
    asyncio.ensure_future(print_in_air(drone))
    asyncio.ensure_future(print_position(drone))
    asyncio.ensure_future(print_altitude(drone))
    asyncio.ensure_future(print_attitude_euler(drone))
    asyncio.ensure_future(print_attitude_quaternion(drone))
    asyncio.ensure_future(print_velocity_ned(drone))
    asyncio.ensure_future(print_home(drone))
    asyncio.ensure_future(print_imu(drone))
    asyncio.ensure_future(print_scaled_imu(drone))

    while True:
        await asyncio.sleep(1)

async def print_battery(drone):
    async for battery in drone.telemetry.battery():
        print(f"[Battery] Remaining: {battery.remaining_percent:6.2%}")

async def print_gps_info(drone):
    async for gps_info in drone.telemetry.gps_info():
        print(f"[GPS] Fix type: {gps_info.fix_type}, Satellites visible: {gps_info.num_satellites}")

async def print_in_air(drone):
    async for in_air in drone.telemetry.in_air():
        print(f"[In Air] {in_air}")

async def print_position(drone):
    async for position in drone.telemetry.position():
        print(f"[Position] Lat: {position.latitude_deg:10.6f}°, Lon: {position.longitude_deg:10.6f}°, Alt: {position.absolute_altitude_m:8.2f} m")

async def print_altitude(drone):
    async for altitude in drone.telemetry.altitude():
        print(
            f"[Altitude] "
            f"Monotonic: {altitude.altitude_monotonic_m:7.2f} m | "
            f"AMSL: {altitude.altitude_amsl_m:7.2f} m | "
            f"Local: {altitude.altitude_local_m:7.2f} m | "
            f"Relative: {altitude.altitude_relative_m:7.2f} m | "
            f"Terrain: {altitude.altitude_terrain_m:7.2f} m | "
            f"Bottom Clearance: {altitude.bottom_clearance_m:7.2f} m"
        )

async def print_attitude_euler(drone):
    async for attitude in drone.telemetry.attitude_euler():
        print(f"[Attitude Euler] Roll: {attitude.roll_deg:7.2f}°, Pitch: {attitude.pitch_deg:7.2f}°, Yaw: {attitude.yaw_deg:7.2f}°")

async def print_attitude_quaternion(drone):
    async for attitude_q in drone.telemetry.attitude_quaternion():
        print(f"[Attitude Quaternion] w: {attitude_q.w:6.3f}  x: {attitude_q.x:6.3f}  y: {attitude_q.y:6.3f}  z: {attitude_q.z:6.3f}")

async def print_velocity_ned(drone):
    async for velocity in drone.telemetry.velocity_ned():
        print(f"[Velocity NED] North: {velocity.north_m_s:7.2f} m/s  East: {velocity.east_m_s:7.2f} m/s  Down: {velocity.down_m_s:7.2f} m/s")

async def print_home(drone):
    async for home in drone.telemetry.home():
        print(f"[Home] Lat: {home.latitude_deg:10.6f}°, Lon: {home.longitude_deg:10.6f}°, Alt: {home.absolute_altitude_m:8.2f} m")

async def print_imu(drone):
    async for imu in drone.telemetry.imu():
        print("[HIGHRES IMU (105)]")
        print(f"  Timestamp : {imu.timestamp_us} us")
        print("  Accel     : "
              f"x={imu.acceleration_frd.forward_m_s2:7.3f} m/s²  "
              f"y={imu.acceleration_frd.right_m_s2:7.3f} m/s²  "
              f"z={imu.acceleration_frd.down_m_s2:7.3f} m/s²")
        print("  Gyro      : "
              f"x={imu.angular_velocity_frd.forward_rad_s:7.3f} rad/s  "
              f"y={imu.angular_velocity_frd.right_rad_s:7.3f} rad/s  "
              f"z={imu.angular_velocity_frd.down_rad_s:7.3f} rad/s")
        print("  Mag       : "
              f"x={imu.magnetic_field_frd.forward_gauss:7.3f} gauss  "
              f"y={imu.magnetic_field_frd.right_gauss:7.3f} gauss  "
              f"z={imu.magnetic_field_frd.down_gauss:7.3f} gauss")
        print(f"  Temp      : {imu.temperature_degc:7.3f} °C")
       


async def print_scaled_imu(drone):
    async for scaled_imu in drone.telemetry.scaled_imu():
        print("[Scaled IMU]")
        print(f"  Timestamp : {scaled_imu.timestamp_us} us")
        print("  Accel     : "
              f"x={scaled_imu.acceleration_frd.forward_m_s2:7.3f} m/s²  "
              f"y={scaled_imu.acceleration_frd.right_m_s2:7.3f} m/s²  "
              f"z={scaled_imu.acceleration_frd.down_m_s2:7.3f} m/s²")
        print("  Gyro      : "
              f"x={scaled_imu.angular_velocity_frd.forward_rad_s:7.3f} rad/s  "
              f"y={scaled_imu.angular_velocity_frd.right_rad_s:7.3f} rad/s  "
              f"z={scaled_imu.angular_velocity_frd.down_rad_s:7.3f} rad/s")
        print("  Mag       : "
              f"x={scaled_imu.magnetic_field_frd.forward_gauss:7.3f} gauss  "
              f"y={scaled_imu.magnetic_field_frd.right_gauss:7.3f} gauss  "
              f"z={scaled_imu.magnetic_field_frd.down_gauss:7.3f} gauss")
        print(f"  Temp      : {scaled_imu.temperature_degc:7.3f} °C")
        print("-" * 50)


if __name__ == "__main__":
    asyncio.run(run())
```