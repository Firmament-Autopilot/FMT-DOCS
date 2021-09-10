
## 硬件在环 (HIL) 仿真

硬件在环（HITL 或 HIL）仿真模式下，FMT 的固件运行在真实的飞控硬件中。这种方法的好处是可以在真实硬件上测试大部分实际飞行代码。

## 设置 HIL

HIL 仿真可以通过在 *fmtconfig.h* 中添加 `#define FMT_USING_HIL` 来开启。然后重新编译系统。

[TBA]