
## 使用J-Link调试
J-Link 是一个强大的调试探针，可以用于调试 FMT-Firmware 代码。请按照以下步骤进行设置：
1. 在你的系统上安装[J-Link Server](https://www.segger.com/downloads/jlink/)。
2. 创建一个新的环境变量`JLINK_SERVER`并将它的值设为J-Link Server的路径， 例如：

Linux:
```
JLINK_SERVER = $JLink/JLinkGDBServerCLExe
```

Windows:
```
JLINK_SERVER = $JLink/JLinkGDBServerCL
```

3. 在 *rtconfig.py* 中设置`BUILD = 'debug'`并重新编译固件。

4. 连接Jlink SWD的引脚（引脚1,7,9,4）到FMU Debug端口。你也可以连接J-Link TX/RX作为控制台使用。
<p align="center">
 <img src="figures/jlink_pinout.png" width="30%">
</p>

5. 在 VSCode 中安装 cortex-debug 扩展。确保安装 v1.4.4 版本或更低版本。这一点至关重要，因为更高版本不支持 arm-gcc 7-2018-q2-update。
<p align="center">
 <img src="figures/cortex-debug.png" width="30%">
</p>

6. 要在 VS Code 中启动调试过程，请点击**Debug Run**按钮。然后，确保选择适合您的目标的配置。在 `.vscode/launch` 文件夹中已经为每个目标添加了调试配置。只需选择相关配置，即可轻松开始调试代码。
<p align="center">
 <img src="figures/jlink1.png" width="30%">
</p>
7. 点击**Start Debugging**按钮。此操作将启动调试固件的下载并在主函数处停止，使您可以检查代码并逐步运行。在左侧，您会发现有几个可用窗口，例如查看变量值或检查外设数据。这些窗口提供了宝贵的见解，并帮助您在调试过程中监控相关信息。
<p align="center">
 <img src="figures/jlink2.png" width="30%">
</p>
## Pixhawk FMU引脚

更多关于pixhawk引脚的信息，请查看如下链接：

[Pixhawk4 pinout](http://www.holybro.com/manual/Pixhawk4-Pinouts.pdf)

[Pixhawk2 pinout](https://docs.px4.io/master/en/flight_controller/pixhawk.html)
