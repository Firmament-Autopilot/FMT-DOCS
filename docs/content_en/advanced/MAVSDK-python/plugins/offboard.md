## API
| API Function                              | Description                                                                 | Example Parameter Type                                    |
| ----------------------------------------- | --------------------------------------------------------------------------- | --------------------------------------------------------- |
| `set_position_ned(PositionNedYaw)`        | Sets the drone's position and yaw in the local NED (North-East-Down) frame. | `PositionNedYaw(x, y, z, yaw)`                            |
| `set_velocity_ned(VelocityNedYaw)`        | Sets the drone's velocity and yaw angle in the local NED frame.             | `VelocityNedYaw(vx, vy, vz, yaw)`                         |
| `set_velocity_body(VelocityBodyYawspeed)` | Sets the drone's velocity relative to its body frame and yaw rate.          | `VelocityBodyYawspeed(vx, vy, vz, yawspeed)`              |
| `set_position_global(PositionGlobalYaw)`  | Sets the target GPS location (longitude, latitude, altitude) and yaw.       | `PositionGlobalYaw(lat, lon, alt, yaw, altitude_type)`    |
| `set_attitude(Attitude)`                  | Sets the drone's attitude (pitch, roll, yaw in degrees) and throttle (0–1). | `Attitude(pitch_deg, roll_deg, yaw_deg, throttle)`        |
| `set_attitude_rate(AttitudeRate)`         | Sets the drone's angular rates (pitch, roll, yaw rate) and throttle.        | `AttitudeRate(pitch_rate, roll_rate, yaw_rate, throttle)` |
| `set_acceleration_ned(AccelerationNed)`   | Sets the drone's acceleration in the local NED frame (m/s²).                | `AccelerationNed(ax, ay, az)`                             |
| `start()`                                 | Starts Offboard control mode.                                               | None                                                      |
| `stop()`                                  | Stops Offboard control mode.                                                | None                                                      |


## Example program
This example program demonstrates the use of the Offboard plugin in MAVSDK-Python. It sequentially connects to the vehicle, starts Offboard mode, arms the drone, and then calls interfaces to set:Position (in both NED and global coordinate frames),Velocity (in NED and body frames),Acceleration,Attitude, and Attitude rate.

Finally, it stops the Offboard control mode.

Before running the script, make sure a mavsdk_server is launched in the terminal to maintain a stable connection with the flight controller.
```
mavsdk_server -p 50051 --system-address serial:///dev/ttyUSB0:115200
```

Open a new terminal, navigate to the MAVSDK-Python directory, and run the example program from the command line.
```
examples/offboard_api_demo.py
```
Create a new file named `offboard_api_demo.py` in the `examples` directory with the following content:
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
                180.0,  # facing south
                PositionGlobalYaw.AltitudeType.REL_HOME
            )
        )
        break  # Exit the loop after receiving one frame of data
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