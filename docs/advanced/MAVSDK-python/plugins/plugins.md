## Plugin introduction
MAVSDK is an object-oriented Software Development Kit (SDK) for MAVLink, providing simple and easy-to-use APIs that allow developers to programmatically control drone flight, navigation, and more. In MAVSDK, **plugins** are a core design concept — each plugin is responsible for implementing a specific category of functionality:

- [action](advanced/MAVSDK-python/plugins/action.md): Supports basic drone operations such as arming, takeoff, and landing.
- [telemetry](advanced/MAVSDK-python/plugins/telemetry.md): Allows users to retrieve telemetry data and status information from the vehicle (e.g., battery status, GPS, RC connection, flight mode), and configure telemetry update rates.
- [offboard](advanced/MAVSDK-python/plugins/offboard.md): Sends offboard control commands to the drone, supporting control in body frame, NED (North-East-Down) frame, and global coordinates.

