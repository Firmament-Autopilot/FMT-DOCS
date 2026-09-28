## API
| API Name                                | API Usage                           | Input / Output Description                                            |
| --------------------------------------- | ----------------------------------- | --------------------------------------------------------------------- |
| `set_rate_position(rate_hz)`            | Set position update rate            | Update frequency (Hz)                                                 |
| `set_rate_altitude(rate_hz)`            | Set altitude update rate            | Update frequency (Hz)                                                 |
| `set_rate_gps_info(rate_hz)`            | Set GPS info update rate            | Update frequency (Hz)                                                 |
| `set_rate_in_air(rate_hz)`              | Set in-air state update rate        | Update frequency (Hz)                                                 |
| `set_rate_attitude_euler(rate_hz)`      | Set Euler attitude update rate      | Update frequency (Hz)                                                 |
| `set_rate_attitude_quaternion(rate_hz)` | Set quaternion attitude update rate | Update frequency (Hz)                                                 |
| `set_rate_velocity_ned(rate_hz)`        | Set NED velocity update rate        | Update frequency (Hz)                                                 |
| `set_rate_home(rate_hz)`                | Set home position update rate       | Update frequency (Hz)                                                 |
| `set_rate_imu(rate_hz)`                 | Set raw IMU data update rate        | Update frequency (Hz)                                                 |
| `set_rate_scaled_imu(rate_hz)`          | Set scaled IMU data update rate     | Update frequency (Hz)                                                 |
| `telemetry.battery()`                   | Subscribe to battery status         | Battery percentage (`remaining_percent`)                              |
| `telemetry.gps_info()`                  | Subscribe to GPS info               | Fix type, number of visible satellites (`fix_type`, `num_satellites`) |
| `telemetry.in_air()`                    | Subscribe to in-air state           | Boolean value                                                         |
| `telemetry.position()`                  | Subscribe to position               | Latitude, longitude, absolute altitude                                |
| `telemetry.altitude()`                  | Subscribe to various altitudes      | Monotonic, AMSL, local, relative, terrain altitude, bottom clearance  |
| `telemetry.attitude_euler()`            | Subscribe to Euler attitude         | Roll, Pitch, Yaw (degrees)                                            |
| `telemetry.attitude_quaternion()`       | Subscribe to quaternion attitude    | w, x, y, z                                                            |
| `telemetry.velocity_ned()`              | Subscribe to NED velocity           | Velocity in North, East, Down directions                              |
| `telemetry.home()`                      | Subscribe to home position          | Latitude, longitude, absolute altitude                                |
| `telemetry.imu()`                       | Subscribe to high-res IMU data      | Acceleration, angular velocity, magnetic field, temperature           |
| `telemetry.scaled_imu()`                | Subscribe to scaled IMU data        | Acceleration, angular velocity, magnetic field, temperature           |


## Example program
The following is an example program to test the Telemetry plugin API. It covers all supported Telemetry message update rate setting functions, retrieval functions, and subscription functions.

Before running the program, it is recommended to use a ground station to take off the drone in Position mode under the SIH simulation and set a waypoint. Alternatively, you can control the SIH simulated drone’s movement using the [action plugin](content_ch/advanced/MAVSDK-python/plugins/action.md) to observe updates in the Telemetry data.

Run a `mavsdk_server` in a terminal to maintain a stable connection with the flight controller.
```
mavsdk_server -p 50051 --system-address serial:///dev/ttyUSB0:115200
```

Open a new terminal, navigate to the MAVSDK-Python directory, and run the example program via the command line.

```
examples/telemetry_api_demo.py
```
Create a new file named `telemetry_api_demo.py` in the `examples` directory with the following content:
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