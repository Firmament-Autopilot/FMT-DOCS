## Plugin简介 
MAVSDK是一个面向MAVLink对象开发的SDK(Software Developmant Kit)，提供了简单易用的API，使开发者能够通过编程控制无人机的飞行、导航等功能。在MAVSDK中，**插件(Plugin)** 是其核心设计理念，每个插件负责实现一类特定的功能：

- [action](content_ch/advanced/MAVSDK-C++/plugins/action.md):支持简单的操作，例如解锁、起飞和降落。
- [telemetry](content_ch/advanced/MAVSDK-C++/plugins/telemetry.md)：允许用户获取飞行器的遥测数据和状态信息（例如电池状态、GPS、遥控器连接、飞行模式等），并设置遥测更新速率。
- [offboard](content_ch/advanced/MAVSDK-C++/plugins/offboard.md)：输入板外控制信息，支持机体系、NED系、global系下的控制。
