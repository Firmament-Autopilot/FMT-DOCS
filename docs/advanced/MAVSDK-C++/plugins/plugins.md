## Plugin introduction
MAVSDK is an SDK (Software Development Kit) designed for MAVLink-based development, providing simple and easy-to-use APIs that allow developers to programmatically control drone flight, navigation, and other functions. In MAVSDK, **plugins** are a core design concept, with each plugin responsible for implementing a specific category of functionality:


- [action](advanced/MAVSDK-C++/plugins/action.md): Supports basic operations such as arming, takeoff, and landing.
- [telemetry](advanced/MAVSDK-C++/plugins/telemetry.md): Allows users to obtain telemetry data and status information from the vehicle (e.g., battery status, GPS, remote control connection, flight mode) and set telemetry update rates.
- [offboard](advanced/MAVSDK-C++/plugins/offboard.md): Enables input of offboard control commands, supporting control in body frame, NED frame, and global frame.
