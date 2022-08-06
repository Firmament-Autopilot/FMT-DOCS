## INS 模型接口

### 输入接口

| Port Name    | Port ID | Bus Type         |
| ------------ | ------- | ---------------- |
| IMU          | 1       | [IMU_Bus](#IMU_Bus)          |
| MAG          | 3       | [MAG_Bus](#MAG_Bus)          |
| Barometer    | 4       | [Barometer_Bus](#Barometer_Bus)    |
| GPS_uBlox    | 5       | [GPS_uBlox_Bus](#GPS_uBlox_Bus)    |
| Rangefinder  | 6       | [Rangefinder_Bus](#Rangefinder_Bus)        |
| Optical_Flow | 7       | [Optical_Flow_Bus](#Optical_Flow_Bus) |

### 输出接口

| Port Name | Port ID | Bus Type    |
| --------- | ------- | ----------- |
| INS_Out   | 1       | [INS_Out_Bus](#INS_Out_Bus) |

### IMU_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | IMU timestamp
float  | gyr_x            | rad/s       | gyroscope x value in body frame
float  | gyr_y            | rad/s       | gyroscope y value in body frame
float  | gyr_z            | rad/s       | gyroscope z value in body frame
float  | acc_x            | m/s2        | accelerometer x value in body frame
float  | acc_y            | m/s2        | accelerometer y value in body frame
float  | acc_z            | m/s2        | accelerometer z value in body frame

### MAG_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | MAG timestamp
float  | mag_x            | gauss       | magnetometer x value in body frame
float  | mag_y            | gauss       | magnetometer y value in body frame
float  | mag_z            | gauss       | magnetometer z value in body frame

### Barometer_Bus

Type   | Name              | Unit       | Comments
-----  | --------------    | ---------- | ----------------
uint32 | timestamp         | ms         | barometer timestamp
float  | pressure          | Pa         | air pressure
float  | temperature       | degree     | temperature

### GPS_uBlox_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | GPS timestamp
uint32 | iTOW             | ms          | GPS time of week
uint16 | year             | year        | year(UTC)
uint8  | month            | month       | month
uint8  | day              | day         | day of month
uint8  | hour             | hour        | hour of day
uint8  | min              | minute      | minute of hour
uint8  | sec              | second      | seconds of minute
uint8  | valid            | -           | valid flags
uint32 | tAcc             | ns          | time accurancy
int32  | nano             | ns          | fraction of second
uint8  | fixType          | -           | GNSS fix Type
uint8  | flags            | -           | fix status flags
uint8  | reserved1        | -           | -
uint8  | numSV            | -           | number of available satelites
int32  | lon              | 1e7 deg     | lontitude
int32  | lat              | 1e7 deg     | latitude
int32  | height           | mm          | height above elipsoid
int32  | hMSL             | mm          | height above mean sea level
uint32 | hAcc             | mm          | horizontal accurancy
uint32 | vAcc             | mm          | vertical accurancy
int32  | velN             | mm/s        | north velocity
int32  | velE             | mm/s        | east velocity
int32  | velD             | mm/s        | down velocity
int32  | gSpeed           | mm/s        | ground speed
int32  | heading          | 1e5 deg     | heading of motion
uint32 | sAcc             | mm/s        | speed accurancy
uint32 | headingAcc       | 1e5 deg     | heading accurancy
uint16 | pDOP             | 1e2 deg     | position DOP
uint16 | reserved2        | -           | -

### Rangefinder_Bus

Type   | Name              | Unit       | Comments
-----  | --------------    | ---------- | ----------------
uint32 | timestamp         | ms         | rangefinder timestamp
float  | distance          | m          | measured distance. -1 indicates invalid

### Optical_Flow_Bus

Type   | Name              | Unit       | Comments
-----  | --------------    | ---------- | ----------------
uint32 | timestamp         | ms         | optical_flow timestamp
float  | vx                | m/s        | relative velosity x in body frame
float  | vy                | m/s        | relative velosity y in body frame
uint8  | quality           | [0 255]    | optical_flow quality, large is better
uint8  | reserved1         | -          | -
uint16 | reserved2         | -          | -

### INS_Out_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | INS output timestamp
single | phi              | rad         | roll
single | theta            | rad         | pitch
single | psi              | rad         | yaw
single[4]| quat           | 1           | attitude quaternion
single | p                | rad/s       | angular rate x in body frame
single | q                | rad/s       | angular rate y in body frame
single | r                | rad/s       | angular rate z in body frame
single | ax               | m/s^2       | specific force x in body frame
single | ay               | m/s^2       | specific force y in body frame
single | az               | m/s^2       | specific force z in body frame
single | vn               | m/s         | north velocity
single | ve               | m/s         | east velocity
single | vd               | m/s         | down velocity
single | airspeed         | m/s         | airspeed
double | lon              | deg         | longitude
double | lat              | deg         | latitude
double | alt              | m           | altitude
single | x_R              | m           | relative position x
single | y_R              | m           | relative position y
single | h_R              | m           | relative height
single | h_AGL            | m           | height above ground level
uint32 |[flag](#flag)     | -           | INS flag
uint32 |[status](#status) | -           | sensor status

### flag

bit    | Comments
-----  | --------------
0      | INS ready
1      | standstill
2      | attitude valid
3      | heading valid
4      | velocity valid
5      | position (WGS84) valid
6      | relative position valid
7      | relative height valid
8      | height above ground valid
9-31   | reserved

### status

bit    | Comments
-----  | --------------
0      | IMU1 available
1      | IMU2 available
2      | magnetometer available
3      | barometer available
4      | gps available
5      | range finder available
6      | optical flow available
7-31   | reserved
