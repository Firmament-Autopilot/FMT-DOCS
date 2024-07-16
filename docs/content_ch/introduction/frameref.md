## 载具/机架

FMT 能够控制多种载具和机架，而且可以轻松添加算法来控制更多类型的载具。

本文档旨在列出这些选项。但请注意，这仍在进行中，并未完全完成。

我们非常欢迎支持更多载具类型的贡献。

#### Multicopter
| Airframe                        | Build Command                             | Description                          |
| ------------------------------- | ----------------------------------------- | ------------------------------------ |
| Quadcopter x, Airframe=1        | scons --vehicle=Multicopter --airframe=1  | ![quad_x](../figures/quad_x.png)     |
| Quadcopter +, Airframe=2        | scons --vehicle=Multicopter --airframe=2  | ![quad_+](../figures/quad_+.png)     |
| Coxial Quadcopter x, Airframe=3 | scons  --vehicle=Multicopter --airframe=3 | ![quad_+](../figures/cox_quad_x.png) |
| Hexacopter x, Airframe=4        | scons --vehicle=Multicopter --airframe=4  | ![quad_+](../figures/hexa_x.png)     |
| Hexacopter +, Airframe=5        | scons --vehicle=Multicopter --airframe=5  | ![quad_+](../figures/hexa_+.png)     |
| Coxial Hexacopter x, Airframe=6 | scons --vehicle=Multicopter --airframe=6  | ![quad_+](../figures/cox_hexa_x.png) |
| Octocopter x, Airframe=7        | scons --vehicle=Multicopter --airframe=7  | ![quad_+](../figures/octo_x.png)     |
| Octocopter +, Airframe=8        | scons --vehicle=Multicopter --airframe=8  | ![quad_+](../figures/octo_+.png)     |

#### Fixwing
| Airframe                     | Build Command                        | Description                       |
| ---------------------------- | ------------------------------------ | --------------------------------- |
| Standard Fixwing, Airframe=1 | scons --vehicle=Fixwing --airframe=1 | ![quad_x](../figures/fixwing.png) |


## Show Vehicle/Airframe Type
当系统启动并运行时，载具和机架信息会显示在系统信息中。您也可以使用 `boot_log` 命令查看系统信息。

```shell

   _____                               __
  / __(_)_____ _  ___ ___ _  ___ ___  / /_
 / _// / __/  ' \/ _ `/  ' \/ -_) _ \/ __/
/_/ /_/_/ /_/_/_/\_,_/_/_/_/\__/_//_/\__/
Firmware.....................FMT FW v0.5.5
Kernel....................RT-Thread v4.0.3
RAM.................................448 KB
Target...........................Amov-ICF5
Vehicle........................Multicopter
Airframe.................................1
INS Model..................Base INS v0.3.2
FMS Model..................Base FMS v0.4.0
Control Model.......Base Controller v0.2.4
Task Initialize:
  mavobc................................OK
  mavgcs................................OK
  logger................................OK
  status................................OK
  vehicle...............................OK
```
