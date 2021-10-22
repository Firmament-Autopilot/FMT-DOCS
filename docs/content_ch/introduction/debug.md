
## 使用J-Link调试

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

3. 在*rtconfig.py*中设置`BUILD = 'debug'`并重新编译固件。

4. 连接Jlink SWD的引脚（引脚1,7,9,4）到FMU Debug端口。你也可以连接J-Link TX/RX作为控制台使用。

<img src="figures/jlink_pinout.png" width="15%">

5. 在VSCode中下载`cortex-debug`插件。

6. 在VSCode中点击**Debug Run**按钮并选择对应的目标板配置。

<img src="figures/jlink1.png" width="20%">

7. 点击**Start Debugging**按钮。

<img src="figures/jlink2.png" width="50%">

## Pixhawk FMU引脚

更多关于pixhawk引脚的信息，请查看如下链接：

[Pixhawk4 pinout](http://www.holybro.com/manual/Pixhawk4-Pinouts.pdf)

[Pixhawk2 pinout](https://docs.px4.io/master/en/flight_controller/pixhawk.html)
