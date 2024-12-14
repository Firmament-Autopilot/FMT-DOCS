## UWB

This chapter explains how to use UWB (Ultra Wide Band) devices for positioning. The Linktrack UWB positioning modules from Nooploop are used in this example. A total of six Linktrack modules are required: one as the TAG, four as ANCHOR, and one as the CONSOLE.

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_usage.png" width="60%">
 </p>

#### Connection

The TAG module is connected to any serial port of the flight controller. Taking the CUAV V5+ as an example, the UWB module is connected to the UART4 port of the flight controller.

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_connect.png" width="60%">
 </p>

#### UWB Ground Station

Connect the UWB Console module to the PC via USB and open the NAssistant software. Select the port corresponding to the UWB Console module and click "Connect."

> NAssistant will automatically detect the baud rate of the UWB module, so no manual configuration is required.

 <p align="left">
 	<img src="./content_ch/advanced/figures/nassistant.png" width="60%">
 </p>

#### TAG
Before installing the TAG unit on the drone, it needs to be configured. Connect it to the computer via USB and use NAssistant to establish a connection. Configure the **role**, **ID**, **protocol**, **baud rate**, etc., as shown in the diagram. Once the configuration is complete, click "Write Parameters" to save the settings.

 <p align="left">
 	<img src="./content_ch/advanced/figures/tag.png" width="60%">
 </p>

#### ANCHOR
The ANCHOR are used to locate the TAG within the area and need to be configured before use. Connect each of the four ANCHOR to the computer and establish a connection using NAssistant. Configure the module **role**, **protocol**, and **baud rate** as shown in the diagram, and set the IDs sequentially to 0, 1, 2, and 3. Mount them on the stands corresponding to base stations A0, A1, A2, and A3.

 <p align="left">
 	<img src="./content_ch/advanced/figures/anchor.png" width="60%">
 </p>

#### CONSOLE
The CONSOLE is used to monitor the data and status of other UWB units, as well as to store calibrated parameters. Connect the CONSOLE to the computer and configure it using NAssistant as described below.

 <p align="left">
 	<img src="./content_ch/advanced/figures/console.png" width="60%">
 </p>

#### Station Calibration

Place the four ANCHOR at the positions shown in the diagram. The recommended station layout range should be greater than 3m x 3m (smaller areas can still be used for positioning). For the 4-base station configuration, the aspect ratio of the layout should generally be less than 3:1.

Ensure that the base ANCHOR are positioned at the same height (height discrepancy should not exceed 20cm), as this will affect positioning accuracy. Additionally, avoid placing the ANCHOR directly on the ground; they should be at least 50cm above the ground or other supporting surfaces. The antennas of the base stations should generally be kept vertically upwards.

 <p align="left">
 	<img src="./content_ch/advanced/figures/anchor_pos.png" width="40%">
 </p>
Power on all the ANCHORs and TAGs, and connect the CONSOLE to the NAssistant. Click on the "Wireless Settings" option to view all the modules that are correctly networked within the system.

- **C0** represents the CONSOLE.
- **A0, A1, A2, A3** are the ANCHORs.
- **T0** is the TAG.

These identifiers will help you verify that all devices are properly connected and communicating within the network.

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_status.png" width="60%">
 </p>
Click on the "One-Click Calibration" button to begin the station calibration. During the calibration process, you will see the parameters on the right side continuously changing. On the 2D View, the relative positions of the four ANCHORs (A0-A3) and the TAG (T0) will be displayed in real-time. This allows you to monitor the calibration progress and ensure proper alignment and positioning of the devices.

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_calib.png" width="60%">
 </p>

  <p align="left">
 	<img src="./content_ch/advanced/figures/2d_pos.jpg" width="60%">
 </p>

> Each time the ANCHOR positions are moved, a recalibration must be performed. 

> CONSOLE, so the CONSOLE must be powered on each time the UWB system is used in order to ensure correct positioning. Without powering the Console, the system will not be able to retrieve the calibration data and perform accurate location tracking.

#### Flight Controller Configuration
The flight controller needs to enable the UWB driver to use UWB for positioning. To do this, open the corresponding BSP's `board.c` file and add `drv_nlink_linktrack_init("serial3");` within the `bsp_initialize()` function. This function initializes the UWB driver and specifies that the device used for communication will be `serial3`.

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_driver.png" width="60%">
 </p>

> You need to comment out any other driver initialization functions that use `serial3` to avoid conflicts. This ensures that `serial3` is exclusively used by the UWB driver.

Power on all the UWB modules and the flight controller. Then, enter the `mcn echo external_pos` command in the flight controller console to check if the positioning data is valid.

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_data.png" width="50%">
 </p>

> By default, the flight controller only uses the UWB horizontal position data and does not utilize the height (altitude) data. If you need to incorporate the height data, additional configuration or modifications may be required.

After confirming that the UWB data is being received correctly, the next step is to calibrate the UWB data on the flight controller. This calibration ensures that the position coordinates of the UWB stations are synchronized with the flight controller's coordinate system. In other words, it enables the flight controller to understand the conversion relationship between the UWB station coordinate system and the flight controller's coordinate system.

First, set the relevant navigation parameters as shown below. Specifically, set `EXT_PSI_MODE` to 1, which indicates that the parameters from `EXT_PSI` will be used as the UWB orientation.

 <p align="left">
 	<img src="./content_ch/advanced/figures/uwb_parameter.png" width="50%">
 </p>
Align the aircraft’s nose to the UWB’s X-axis (the direction from A0 to A3). Then, enter the `calib extpos` command in the flight controller console to calibrate. This command will retrieve the current aircraft heading and store it in the `EXTPOS_PSI` parameter (in radians). After calibration, make sure to enter the `param save` command to save the calibration parameters.

 <p align="left">
 	<img src="./content_ch/advanced/figures/calib_extpos.png" width="50%">
 </p>

> Before calibrating the UWB orientation, make sure that the flight controller's magnetometer (compass) has been properly calibrated to obtain the correct heading. 

> If the position of any UWB station changes, a recalibration must be performed. 