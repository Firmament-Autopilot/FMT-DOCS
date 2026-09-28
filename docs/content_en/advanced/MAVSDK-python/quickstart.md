## MAVSDK-Python introduction
MAVSDK-Python is a modern Python SDK for controlling MAVLink drones (such as PX4). It uses an asynchronous programming model, offers a simple and user-friendly interface, and supports rapid implementation of features like automatic takeoff and landing, waypoint navigation, and status monitoring. It is widely used in drone development, testing, and mission automation scenarios.

## Environment Configuration
- Linux

Let’s take installing and using MAVSDK-Python on Ubuntu 20.04 as an example.

1.Install the `mavsdk-python` library
```
pip3 install mavsdk==2.8.4
```

> After installation, add the following line to the end of your ~/.bashrc file to include the MAVSDK executable path in the environment variable PATH:```export PATH=$PATH:/home/ajex/.local/lib/python3.8/site-packages/mavsdk/bin```

> Save and exit the file, then run the following command in the terminal to apply the changes immediately:```source ~/.bashrc```


2.Clone MAVSDK-Python(2.8.4)
```
git clone --branch 2.8.4 --recursive https://github.com/mavlink/MAVSDK-Python.git
```
3.Install ipython
```
sudo apt install python3-pip
pip3 install ipython
```

## Run mavsdk-python
Next, taking the official FMT flight controller S1 as an example, we will demonstrate how to run the takeoff_and_land.py example from the official mavsdk-python project, and how to use IPython to control the drone for takeoff and landing.

Before starting, compile and flash the pure hardware-in-the-loop simulation (SIH) firmware for the quadcopter.
```
cd .\target\sieon\s1\
scons -j4 --sim=SIH
python.exe ./uploader.py
```
After flashing the firmware, connect the flight controller to the ground station, and use a TTL-to-USB cable to connect the host computer to the flight controller’s TELEM2 port (for detailed operation, please refer to [MAVSDK-C++/Connect to Flight Controller](advanced/MAVSDK-C++/quickstart.md)).

In the Ubuntu 20.04 terminal, run a mavsdk_server to maintain a stable connection with the flight controller (keep it running throughout; all example programs by default use this mavsdk_server).
```
mavsdk_server -p 50051 --system-address serial:///dev/ttyUSB0:115200
```
### Run takeoff_and_land.py
Open the downloaded MAVSDK-Python project in VSCode, then modify the examples/takeoff_and_land.py file (alternatively, you can create a new Python file and run it).
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
After entering the MAVSDK-Python directory, run the takeoff_and_land.py script using the following command:
```
examples/takeoff_and_land.py 
```
### ipython
Open a new terminal, type ipython, then enter:
```
ajex@ubuntu:~$ ipython
Python 3.8.10 (default, Mar 18 2025, 20:04:55) 
Type 'copyright', 'credits' or 'license' for more information
IPython 8.12.3 -- An enhanced Interactive Python. Type '?' for help.

In [1]: from mavsdk import System
```
Then create a `System` object (named `drone` in this example), specifying the connection to the locally running `mavsdk_server`, and connect it to the drone. This object acts as our “handle” to access the rest of MAVSDK’s functionality.
```
In [2]: drone = System(mavsdk_server_address="localhost", port=50051)

In [3]: await drone.connect()
```
Once connected successfully, we can use the corresponding MAVSDK commands to arm the drone, take off, and land.
```
In [5]: await drone.action.arm()

In [6]: await drone.action.takeoff()

In [7]: await drone.action.land()
```