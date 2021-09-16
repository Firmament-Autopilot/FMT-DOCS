
## Hardware-In-The-Loop (HIL) Simulation

Hardware-in-the-Loop (HITL or HIL) is a simulation mode in which normal FMT firmware is run on real flight controller hardware. This approach has the benefit of testing most of the actual flight code on the real hardware.

## Setting Up HIL

HIL simulation is enabled by adding `#define FMT_USING_HIL` in *fmtconfig.h*. Then rebuild the system.

[TBA]