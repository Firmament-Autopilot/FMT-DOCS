## 地面控制站
FMT 完全支持标准的 Mavlink 协议，使其能够与任何支持 Mavlink 的地面控制站 (GCS) 无缝集成。理论上，这种兼容性意味着 FMT 可以与实现 Mavlink 协议的各种 GCS 软件协同工作。这种灵活性使用户可以选择最适合其需求和偏好的 GCS。

QGroundControl (QGC) 3.5.6 是 FMT 的首选 GCS，确保在操作和配置 FMT 驱动的飞行器时提供流畅高效的体验。

需要注意的是，FMT 没有定制版本的 QGC，而是使用 QGC 的 PX4 版本。因此，在使用 QGC PX4 与 FMT 时，您可能会遇到一些错误，例如缺少参数。这是正常的，因为 QGC PX4 主要为 PX4 设计，可能没有完全针对 FMT 进行优化。尽管存在这些错误，FMT 仍然可以正常运行，开发人员也在不断努力改进 FMT 和 QGC 之间的集成，以提供更顺畅的使用体验。
<p align="center">
 <img src="figures\qgc.png" width="60%">
</p>

## 连接至地面控制站


要建立飞控和地面控制站 (GCS) 之间的连接，可以使用 USB 电缆或遥测设备。

对于标准的 Pixhawk 硬件，QGC 可以自动检测并连接到飞控，简化了设置过程。

然而，对于像 ICF5 这样的其他硬件配置，您可能需要手动创建通信链路。这涉及配置通信参数，例如波特率和端口，以在飞控和 GCS 之间建立成功连接。
<p align="center">
 <img src="figures\qgc_connect.png" width="60%">
</p>

>对于 USB 连接，不要纠结于波特率的选择。

>在通过串口连接飞控时，波特率请按照 *sysconfig.toml* 文件中所设置的对应串口的参数值来进行配置。