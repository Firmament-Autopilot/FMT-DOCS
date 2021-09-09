# Introduction

> An advanced autopilot system designed for Model-Based Design.

## What is Firmament?

Firmament (FMT) is an advanced autopilot system which is develop for Model-Based Design (MBD). 

The system is mainly composed of two parts.

- [FMT-Firmware](https://github.com/Firmament-Autopilot/FMT-Firmware): A stable and high performance embedded system designed with C/C++.
- [FMT-Model](https://github.com/Firmament-Autopilot/FMT-Model): A simulation framework with algorithm libraries designed with MATLAB/Simulink.

## Motivation

Model-based design provides an efficient approach for establishing a complex and safe control system. It has been widely used in many industries, primarily in the automotive and aerospace sectors. Currently there is few open-source autopilot system which is using Model-based Design and FMT-Autopilot is born to this. FMT provides an ideal platform to develop and test your own state-of-the-art algorithm and deploy it on various hardware or just run in simulator. FMT provides very powerful simulation capability to makes code debug and optimization much easier.

## Why Firmament?

- High development efficency achieved with model-based deisign and debug easier.
- A stable and high performance embedded system designed with C/C++.
- A powerful simulation framework with various algorithm library designed with MATLAB/Simulink.
- Auto code generation from Simulink model adapted to different hardware platforms (ARM, AMD, Intel, etc).
- Excellent real-time performance based on [RT-Thread](https://www.rt-thread.io/) RTOS with active community and large number of third-party components.
- Support with most widely used open-source hardware Pixhawk (Both FMUv2 and FMUv5 are supported).
- Cross-platform toolchain support with Windows/Linux/Mac.
- Support with Mavlink v1.0/v2.0.

## Community

**Maintainer**:
- Jiachi Zou

**Contributor**:
- AmovLab
- weety
- wenzhi
- yangjion
- MOONCAKE_G
