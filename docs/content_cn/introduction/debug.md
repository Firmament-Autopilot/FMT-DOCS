
## 使用J-Link调试
J-Link 是一个功能强大的调试设备，可以用于调试 FMT-Firmware 代码，进行单步代码运行、打断点或者查看变量信息等。请按照以下步骤进行设置：
1. 在你的系统上安装[J-Link Server](https://www.segger.com/downloads/jlink/)。

2. 创建一个新的环境变量`JLINK_SERVER`并将它的值设为J-Link Server的路径， 例如：

   **Linux/Mac**:

   ```
   JLINK_SERVER = $JLink/JLinkGDBServerCLExe
   ```

   **Windows**:

   ```
   JLINK_SERVER = $JLink/JLinkGDBServerCL
   ```
   > 请将`$JLink`替换为Jlink Server的完整安装路径


3. 在 *rtconfig.py* 中设置`BUILD = 'debug'`并重新编译固件。

   <p align="center">
    <img src="content_cn/introduction/figures/rtconfig_debug.png" width="30%">
   </p>

4. 连接Jlink SWD的引脚（引脚1,7,9,4）到飞控Debug端口。你也可以连接J-Link TX/RX作为控制台使用。

<p align="center">
 <img src="content_cn/introduction/figures/jlink_pinout.png" width="30%">
</p>

5. 在 VSCode 中安装 cortex-debug 扩展。确保安装 `v1.4.4 `版本或更低版本。这一点至关重要，因为更高版本不支持当前试用的编译器版本（ arm-gcc 7-2018-q2-update）
<p align="center">
 <img src="content_cn/introduction/figures/cortex-debug.png" width="30%">
</p>

6. 要在 VS Code 中启动调试过程，请选择`Run and Debug`菜单。然后**选择对应的目标的配置**。点击**Start Debugging**按钮，即可下载代码并开启调试界面。
<p align="center">
 <img src="content_cn/introduction/figures/jlink_debug.png" width="30%">
</p>

