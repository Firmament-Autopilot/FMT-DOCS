
## 系统配置

在配置 FMT 系统模块时，避免重新编译整个系统是很必要的。FMT是一个高度可定制化的系统，并提供了一个[TOML](https://toml.io/en/)文件来配置系统组件。这将给用户根据需求来配置系统提供极大的灵活性。

当FMT系统运行起来的时候，它会去自动寻找板载文件系统上的`/sys/sysconfig.toml`配置文件。如果这个文件存在，则会加载里面的配置项。如果这个文件不存在，则会使用`default_config.h`中固化的默认系统配置。注意，默认配置只开启了有限的功能，遥控和电机输出功能可能未开启。

 <p align="center">
  <img src="./figures/config_flow.png" width="30%">
 </p>


每个BSP下的config目录都包含一个默认的配置文件 *sysconfig.toml* 。为了开启FMT的所有功能，用户需要将 *sysconfig.toml* 配置文件上传到飞控上SD卡的 */sys* 目录。

---

上传配置文件有两种方式：

1. QGC Onboard File(QGC 3.5.6 only):如果您正在使用QGround-Control 3.5.6，您可以利用其提供的 "Onboard Files" 功能直接将配置文件上传到系统中。这样做简化了整个配置过程，并且能够通过 QGC 轻松地管理配置。

 <p align="center">
  <img src="./figures/onboard_files.png" width="50%">
 </p>


2. SD 卡阅读器:可以使用 SD 卡阅读器将配置文件上传到系统中。只需将配置文件复制到 SD 卡上，然后插入系统中即可。这种方法不依赖于 QGC 版本，并适用于无法访问 QGC Onboard Files 的系统。

更多的配置信息内容，请参考接下来的章节。