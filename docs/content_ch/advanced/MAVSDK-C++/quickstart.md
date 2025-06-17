## MAVSDK介绍
MAVSDK 是一个跨平台的开发库集合，通过提供简单的 API，简化了与 MAVLink 系统的交互，适用于无人机、地面站及移动设备的开发，可实现信息获取、遥测、任务控制等多种功能。
## 环境配置
- Linux

    我们以在ubuntu20.04下安装使用MAVSDK为例。
    1. 安装依赖项与工具包
        ```
        sudo apt-get update -y
        sudo apt-get install python3-pip -y
        sudo apt-get install cmake build-essential colordiff git doxygen -y
        ```
    2. 克隆MAVSDK(v2.12版本)存储库到本地
        ```
        git clone -b v2.12 https://github.com/mavlink/MAVSDK.git --recursive --shallow-submodule
        ```
    5. 通过以下命令构建（Debug/Release）C++库
        ```
        cd MAVSDK
        cmake -DCMAKE_BUILD_TYPE=Debug -DBUILD_SHARED_LIBS=ON -Bbuild/default -H.
        cmake --build build/default
        ```
        > Debug 版本适用于调试和开发阶段,Release 版本适合测试和部署阶段。
    6. 安装SDK(Software Development Kit)
        - 安装在系统范畴(推荐)
            - 系统范畴的安装将 SDK 的头文件和二进制文件复制到平台的标准系统范围位置（例如，在 Ubuntu Linux 上，这是 /usr/local/）。
            - 在系统范畴安装SDK
                ```
                sudo cmake --build build/default --target install # sudo is required to install to system directories!

                # only for first installation 
                sudo ldconfig  # update linker cache
                ```
                > 系统范畴的安装会覆盖之前安装的 SDK 版本
        - 安装在本地
            - 本地安装将 SDK 的头文件/库复制到 SDK 源目录内用户指定的位置
            - 在 Linux/macOS 上，使用 CMAKE_INSTALL_PREFIX 变量指定一个相对于调用 cmake 的文件夹的路径（或绝对路径）。例如，要安装到 MAVSDK/install/ 文件夹中，你可以使用以下命令
            ```
            cmake -DCMAKE_BUILD_TYPE=Release -DBUILD_SHARED_LIBS=ON -DCMAKE_INSTALL_PREFIX=install -Bbuild/default -H.
            cmake --build build/default --target install
            ```
            > 如果你在没有指定```CMAKE_INSTALL_PREFIX```的情况下运行了cmake,你可以通过 ```rm -rf build/default```来清除所构建的文件

## 连接飞控
- Mavproxy配置

    经过测试，推荐配置**sysconfig.toml**文件中的**Mavproxy**设备配置项中 **chan = 1** 的波特率为 ```115200```
    ```
    # Mavproxy Device Configuration
    [mavproxy]
        [[mavproxy.devices]]
        chan = 0
        type = "serial"
        name = "serial1"
        baudrate = 57600

        [[mavproxy.devices]]
        chan = 0
        type = "usb"
        name = "usbd0"
        auto-switch = true

        [[mavproxy.devices]]
        chan = 1
        type = "serial"
        name = "serial2"
        baudrate = 115200
    ```
- 端口连接

    以CUAV_v5+为例，使用一根TTL转USB线连接电脑以及飞控```TELEM2```端口，连接后打开控制台输入
    ```
    ls /dev/ttyUSB*
    ```
    查看USB端口名
    ```
    rock@rock-ThinkBook-16-G6-IMH:~$ ls /dev/ttyUSB*
    /dev/ttyUSB0
    ```
    端口名为```/dev/ttyUSB0```
    > PS：若无端口号显示，请根据相关教程配置USB驱动
    
    随后在控制台输入

    ```
    cu -l /dev/ttyUSB0 -s 115200
    ```
    若能接收到心跳包则飞控和电脑可以正确连接并传输Mavlink消息
    ```
    rock@rock-ThinkBook-16-G6-IMH:~$ cu -l /dev/ttyUSB0 -s 115200
    Connected.
    �	�
             �A�	�
                     ���	�
                             �U�	�
                                     ���	�
                                             �i�	�
                                                     ���	�
                                                             �}�	�
                                                                     �>�	�
                                                                             ���	�
                                                                                   �
    ```
    > 若USB设备无读写权限并显示permission denied错误，需要在命令行中输入``` sudo chmod 666 /dev/ttyUSB0 ```来赋予设备读写的权限。

## 编译示例

1. 示例可以在 examples 目录中找到
    ```
    cd examples
    ```
2. 编译 ***takeoff_and_land*** 示例
    ```
    cd takeoff_and_land/
    cmake -Bbuild build -H.
    cmake --build build -j4
    ```

## 运行takeoff_and_land 示例程序

推荐在```SIH```仿真下测试MAVSDK,主机通过TTL转USB线连接飞控的TELEM2端口，用于沟通MAVSDK，同时使用Typec连接电脑与飞控，在地面站上监测```SIH```仿真飞行器的行为表现。
<p align="center">
 <img src="figures/mavsdk_connection.png" width="45%">
</p>
运行示例程序

```
cd examples/takeoff_and_land/
build/takeoff_and_land serial:///dev/ttyUSB0:115200
```

> 如果显示连接超时再次执行命令即可

接下来以```takeoff_and_land```示例程序为例，来分析一个完整的MAVSDK-C++程序由是如何建立以及运行的，以此来建立对MAVSDK的初步概念。


这部分代码主要包含了头文件的引入和一些命名空间的使用。

```
    #include <chrono>  // 引入用于处理时间和时间间隔的标准库，主要用于延迟（如 sleep_for）和时间计算
    #include <cstdint> // 引入用于定义固定大小的整数类型的标准库，例如 int32_t, uint8_t 等
    #include <mavsdk/mavsdk.h> // 引入 MAVSDK 主库，用于与无人机通信和控制
    #include <mavsdk/plugins/action/action.h> // 引入 MAVSDK 的 Action 插件库，用于执行无人机的动作（如起飞、着陆、悬停等）
    #include <mavsdk/plugins/telemetry/telemetry.h> // 引入 MAVSDK 的 Telemetry 插件库，用于获取无人机的遥测数据（如位置、姿态、速度等）
    #include <iostream>  // 引入标准输入输出库，用于在终端输出调试信息
    #include <future>    // 引入用于处理异步操作的库，该示例程序中暂未使用，通常用于多线程编程中
    #include <memory>    // 引入智能指针库，提供内存管理支持
    #include <thread>    // 引入线程库，用于执行多线程操作

    using namespace mavsdk;  // 使用 mavsdk 命名空间，简化后续代码中对 mavsdk 库中类和函数的调用
    using std::chrono::seconds;  // 使用 std::chrono::seconds，表示秒的时间单位，简化时间延迟代码中的使用
    using std::this_thread::sleep_for; // 使用 sleep_for 函数，它可以使当前线程休眠指定的时间
```
这一段代码定义了一个 usage 函数，用于输出程序的使用说明。当用户在命令行中运行程序时，如果没有正确输入参数或需要查看如何使用该程序时，程序会调用这个函数来显示帮助信息。
```
    void usage(const std::string& bin_name)
    {
        std::cerr << "Usage : " << bin_name << " <connection_url>\n"
                  << "Connection URL format should be :\n"
                  << " For TCP : tcp://[server_host][:server_port]\n"
                  << " For UDP : udp://[bind_host][:bind_port]\n"
                  << " For Serial : serial:///path/to/serial/dev[:baudrate]\n"
                  << "For example, to connect to the simulator use URL: udpin://0.0.0.0:14540\n";
    }
```

以下是main函数中的内容

首先检查命令行参数是否正确，若不正确，则会打印usage函数信息。
```
    if (argc != 2) {
        usage(argv[0]);
        return 1;
    }
```
连接飞控
```
    Mavsdk mavsdk{Mavsdk::Configuration{ComponentType::CompanionComputer}};  //设置MAVLink message sender的身份，在这里我们希望电脑作为机载电脑运行MAVSDK沟通飞控
    ConnectionResult connection_result = mavsdk.add_any_connection(argv[1]); //根据设定的connection路径添加连接关系

    if (connection_result != ConnectionResult::Success) { //检查是否成功建立连接关系
        std::cerr << "Connection failed: " << connection_result << '\n';
        return 1;
    }

    auto system = mavsdk.first_autopilot(3.0); //在已有连接关系中寻找并添加第一个找到的autopilot
    if (!system) {
        std::cerr << "Timed out waiting for system\n";
        return 1;
    }
```
初始化插件(plugins),这里初始化了telemetry以及action插件
```
    auto telemetry = Telemetry{system.value()};
    auto action = Action{system.value()};
```
以1Hz的频率监听无人机高度信息
```
    const auto set_rate_result = telemetry.set_rate_position(1.0);
    if (set_rate_result != Telemetry::Result::Success) {
        std::cerr << "Setting rate failed: " << set_rate_result << '\n';
        return 1;
    }
```
当监听的高度信息更新时，打印高度信息
```
    telemetry.subscribe_position([](Telemetry::Position position) {
        std::cout << "Altitude: " << position.relative_altitude_m << " m\n";
    });
```
> 获取任何telemetry信息前，都需要以一定的频率监听该telemetry信息

检查无人机传感器数据是否符合解锁要求
```
    while (telemetry.health_all_ok() != true) {
        std::cout << "Vehicle is getting ready to arm\n";
        sleep_for(seconds(1));
    }
```
发布解锁指令，并根据返回的结果判断无人机是否正常解锁
```
    std::cout << "Arming...\n";
    const Action::Result arm_result = action.arm();

    if (arm_result != Action::Result::Success) {
        std::cerr << "Arming failed: " << arm_result << '\n';
        return 1;
    }
```

发布起飞指令，并根据返回结果判断无人机是否响应指令起飞
```
    std::cout << "Taking off...\n";
    const Action::Result takeoff_result = action.takeoff();
    if (takeoff_result != Action::Result::Success) {
        std::cerr << "Takeoff failed: " << takeoff_result << '\n';
        return 1;
    }
```
等待十秒然后发出降落指令，并监听无人机是否正确响应降落指令。在这期间若无人机完成起飞，则会进入Hold模式，若等待时间过短，无人机未完成起飞也会响应并执行降落指令
```
    sleep_for(seconds(10));

    std::cout << "Landing...\n";
    const Action::Result land_result = action.land();
    if (land_result != Action::Result::Success) {
        std::cerr << "Land failed: " << land_result << '\n';
        return 1;
    }
```

检查无人机是否还在空中
```
   while (telemetry.in_air()) {
        std::cout << "Vehicle is landing...\n";
        sleep_for(seconds(1));
    }
    std::cout << "Landed!\n";
```

落地后无人机自动上锁，示例程序中等待一会儿，随后结束示例程序
```
    sleep_for(seconds(3));
    std::cout << "Finished...\n";

    return 0;
```