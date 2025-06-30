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
goto_location(latitude_deg, longitude_deg, absolute_altitude_m, yaw_deg)        | send go to given location command     | The desired longitude, latitude, altitude, and yaw angle during operation
set_takeoff_altitude(altitude)  | set the takeoff altitude              | Target takeoff altitude
set_current_speed(speed_m_s)     | set flight speed                      | Target cruising speed

> In FMT, the yaw_deg parameter of the goto_location() function does not influence the vehicle's yaw during its movement to the target location.

## Example program

The following is an example program used to test the Action plugin API.

The sequence of commands issued by the program is: **arm**, **set takeoff altitude**, **take off**, **set flight speed**, **go to a specified location**, **hold**, **return to launch**, **land**, **disarm**, and finally **reboot**.

Before running the program, start a `mavsdk_server` in the terminal to maintain a stable connection with the flight controller.

```
mavsdk_server -p 50051 --system-address serial:///dev/ttyUSB0:115200
```
Open a new terminal, navigate to the **MAVSDK-Python** directory, and start the example program using the command line.

```
examples/action_api_demo.py
```
Create a new file named action_api_demo.py in the examples directory with the following content:
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