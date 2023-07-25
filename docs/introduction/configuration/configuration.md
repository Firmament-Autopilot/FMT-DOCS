
## System Configuration

It's necessary to configure the system modules without recompiling the whole system. FMT is a highly customizable system and provides a way to configure modules via a [TOML](https://toml.io/en/) file, which gives users great flexibility to configure the system to their desired settings. 

When the FMT system is started up, it will automatically look for the system configuration file `/sys/sysconfig.toml` on the onboard file system. If this file exists, the system will load the configuration settings within it. However, if the file is not present, the system use the default configuration which is hardcoded in `default_config.h`. Note that the default configuration has a limited set of functions, with both the rc and actuator modules disabled.

Each BSP contains a default *sysconfig.toml* under the config directory. To ensure that the FMT system is fully functional, users must upload a *sysconfig.toml* file to the */sys* folder onboard. This file contains the necessary settings for modules to be functional.

- [Amov ICF5 Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/amov/icf5/config/sysconfig.toml)
- [CUAV V5+ Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/amov/icf5/config/sysconfig.toml)
- [HEX Cubeorange Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/cubepilot/cubeorange/config/sysconfig.toml)
- [Pixhawk 4 Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v5/config/sysconfig.toml)
- [Pixhawk 2.4.6 Configuration](https://github.com/Firmament-Autopilot/FMT-Firmware/blob/master/target/pixhawk/fmu-v2/config/sysconfig.toml)

To upload the configuration file, you have two options:

1. **QGC Onboard Files (QGC 3.5.6 only)**: If you are using QGroundControl (QGC) version 3.5.6, you can utilize the "Onboard Files" feature to upload the configuration file directly to the system. This simplifies the process and allows you to manage the configuration easily through QGC.

    <p align="center">
      <img src="./figures/onboard_files.png" width="30%">
    </p>

2. **SD Card Reader**: Alternatively, you can use an SD card reader to upload the configuration file to the system. Simply copy the configuration file onto the SD card and insert it into the system. This method works independently of the QGC version and is suitable for systems without access to QGC Onboard Files.