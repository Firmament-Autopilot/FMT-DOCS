<!-- docs/_sidebar.md -->

- [简介](content_ch/)

- 基础入门

  - [快速上手](content_ch/introduction/quickstart.md)
  - [地面控制站](content_ch/introduction/gcs.md)
  - [载具/机架](content_ch/introduction/frameref.md)
  - [系统配置](content_ch/introduction/configuration/configuration.md)
	- [控制台配置](content_ch/introduction/configuration/console_config.md)
	- [Mavproxy配置](content_ch/introduction/configuration/mavproxy_config.md)
	- [遥控配置](content_ch/introduction/configuration/pilot_cmd_config.md)
	- [输出配置](content_ch/introduction/configuration/actuator_config.md)

  - 传感器校准
  
    - [陀螺仪校准](content_ch/introduction/calibration/gyro_calib.md)
    - [加速度计校准](content_ch/introduction/calibration/accel_calib.md)
    - [罗盘校准](content_ch/introduction/calibration/mag_calib.md)
    - [水平校准](content_ch/introduction/calibration/level_calib.md)
    - [遥控校准](content_ch/introduction/calibration/rc_calib.md)
    - [电调校准](content_ch/introduction/calibration/esc_calib.md)
    - [空速校准](content_ch/introduction/calibration/airspeed_calib.md)
  - [首次飞行](content_ch/introduction/first_flight.md)
  - [参数调整](content_ch/introduction/param_tuning.md)
  - [调试](content_ch/introduction/debug.md)
  - [日志](content_ch/introduction/logging.md)
  - [命令行](content_ch/introduction/command.md)

- 硬件设备

  - [整机](content_ch/hardware/complete_vehicle.md)
  - [飞行控制器](content_ch/hardware/flight_controller.md)
  - [外设](content_ch/hardware/peripheral.md)
  - [机载电脑](content_ch/hardware/companion_computer.md)

- 基于模型设计

  - [介绍](content_ch/mbd/introduction.md)
  - [快速开始](content_ch/mbd/quickstart.md)
  
  - 模型接口

    - [FMS 接口](content_ch/mbd/interface/fms_interface.md)
    - [INS 接口](content_ch/mbd/interface/ins_interface.md)
    - [Controller 接口](content_ch/mbd/interface/controller_interface.md)
    - [Plant 接口](content_ch/mbd/interface/plant_interface.md)

  - [添加新模型](content_ch/mbd/new_model.md)
  - [生成代码](content_ch/mbd/codegen.md)
  - [部署代码](content_ch/mbd/code_deploy.md)

- 仿真

  - [模型在环仿真(MIL)](content_ch/simulation/MIL.md)
  - [数据放在仿真(Data Sim)](content_ch/simulation/openloop.md)
  - [纯硬件仿真(SIH)](content_ch/simulation/SIH.md)
  - [软件在环仿真(SIL)](content_ch/simulation/SIL.md)
  - [硬件在环仿真(HIL)](content_ch/simulation/HIL.md)

- 开发者
    - [任务](content_ch/developer/tasks.md)

    - 模块
      - [uMCN](content_ch/developer/modules/uMCN.md)
      - [Param](content_ch/developer/modules/param.md)
      - [Mlog](content_ch/developer/modules/mlog.md)
      - [Mavproxy](content_ch/developer/modules/mavproxy.md)
    - [驱动](content_ch/developer/drivers.md)
    - [构建系统](content_ch/developer/build.md)
    - [贡献](content_ch/developer/contribution.md)
- 进阶
    - [板外控制](content_ch/advanced/offboard.md)
    - [任务规划](content_ch/advanced/mission.md)
