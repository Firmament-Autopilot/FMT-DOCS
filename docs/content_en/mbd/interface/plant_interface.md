## Plant Model Interface

### Input Interface

| Port Name   | Port ID | Bus Type        |
| ----------- | ------- | :-------------- |
| Control_Out | 1       | [Control_Out_Bus](mbd/interface/controller_interface.md#Control_Out_Bus) |

### Output Interface

| Port Name       | Port ID | Bus Type            |
| --------------- | ------- | ------------------- |
| Plant_States    | 1       | [Plant_States_Bus](#Plant_States_Bus)    |
| Extended_States | 2       | [Extended_States_Bus](Extended_States_Bus) |
| IMU             | 3       | [IMU_Bus](mbd/interface/ins_interface.md#IMU_Bus) |
| MAG             | 4       | [MAG_Bus](mbd/interface/ins_interface.md#MAG_Bus)             |
| Barometer       | 5       | [Barometer_Bus](mbd/interface/ins_interface.md#Barometer_Bus)       |
| GPS             | 6       | [GPS_uBlox_Bus](mbd/interface/ins_interface.md#GPS_uBlox_Bus)       |

### Control_Out_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp     | ms          | control output timestamp
uint16[16] | actuator_cmd          | [1000 2000] | actuator commands

### Plant_States_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | plant states timestamp
single | phi              | rad         | roll
single | theta            | rad         | pitch
single | psi              | rad         | yaw
single | p                | rad/s       | angular rate x in body frame
single | q                | rad/s       | angular rate y in body frame
single | r                | rad/s       | angular rate z in body frame
single | ax               | m/s^2       | specific force x in earth frame
single | ay               | m/s^2       | specific force y in earth frame
single | az               | m/s^2       | specific force z in earth frame
single | vn               | m/s         | north velocity
single | ve               | m/s         | east velocity
single | vd               | m/s         | down velocity
single | x_R              | m           | relative position x
single | y_R              | m           | relative position y
single | h_R              | m           | relative height
double | lon              | deg         | longitude
double | lat              | deg         | latitude
double | alt              | m           | altitude
double | lon_ref          | deg         | reference longitude
double | lat_ref          | deg         | reference latitude
double | alt_ref          | m           | reference altitude

### Extended_States_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
float  | temprature       | degree      | plant temprature
float[8] | prop_vel       | RPM         | propeller velocity
float[4] | quat           | 1           | attitude quaternion
float[3][3] | M_BO        | -           | DCM from earth frame to body frame
float[3][3] | M_OB        | -           | DCM from body frame to earth frame
