## FMS Model Interface

### Input Interface

| Port Name   | Port ID | Bus Type        |
| ----------- | ------- | --------------- |
| Pilot_Cmd   | 1       | [Pilot_Cmd_Bus](#Pilot_Cmd_Bus)   |
| GCS_Cmd   | 2       | [GCS_Cmd_Bus](#GCS_Cmd_Bus)   |
| Auto_Cmd   | 3       | [Auto_Cmd_Bus](#Auto_Cmd_Bus)   |
| Mission_Data   | 4       | [Mission_Data_Bus](#Mission_Data_Bus)   |
| INS_Out     | 5       | [INS_Out_Bus](mbd/interface/ins_interface.md#INS_Out_Bus)     |
| Control_Out | 6       | [Control_Out_Bus](mbd/interface/controller_interface.md#Control_Out_Bus) |

### Output Interface

| Port Name | Port ID | Bus Type    |
| --------- | ------- | ----------- |
| FMS_Out   | 1       | [FMS_Out_Bus](#FMS_Out_Bus) |

### Pilot_Cmd_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | output timestamp
single | stick_yaw        | [-1 1]      | stick value of yaw channel
single | stick_throttle   | [-1 1]      | stick value of throttle channel
single | stick_roll       | [-1 1]      | stick value of roll channel
single | stick_pitch      | [-1 1]      | stick value of pitch channel
uint32 | mode             | enum PilotMode | pilot mode:<br>0: None<br>1: Manual<br>2: Acro<br>3: Stabilize<br>4: Altitude<br>5: Position<br>6: Mission<br>7: Offboard
uint32 | cmd_1            | enum FMS_Cmd | fms command_1 (event command):<br>0: None<br>1000: CMD_PreArm<br>1001: CMD_Arm<br>1002: CMD_Disarm<br>1003: CMD_Takeoff<br>1004: CMD_Land<br>1005: CMD_Return<br>1006: CMD_Pause<br>1007: CMD_Continue
uint32 | cmd_2            | -           | fms command_2 (status command)

### GCS_Cmd_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | output timestamp
uint32 | mode             | enum PilotMode | pilot mode:<br>0: None<br>1: Manual<br>2: Acro<br>3: Stabilize<br>4: Altitude<br>5: Position<br>6: Mission<br>7: Offboard
uint32 | cmd_1            | enum FMS_Cmd | fms command_1 (event command):<br>0: None<br>1000: CMD_PreArm<br>1001: CMD_Arm<br>1002: CMD_Disarm<br>1003: CMD_Takeoff<br>1004: CMD_Land<br>1005: CMD_Return<br>1006: CMD_Pause<br>1007: CMD_Continue
uint32 | cmd_2            | -           | fms command_2 (status command)
single[7] | param | - | command parameters

### Auto_Cmd_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | output timestamp
single | p_cmd             | rad/s | rate x command in body frame
single | q_cmd             | rad/s | rate y command in body frame
single | r_cmd             | rad/s | rate z command in body frame
single | phi_cmd             | rad | roll command
single | theta_cmd             | rad | pitch command
single | psi_cmd             | rad | yaw command
single | psi_rate_cmd             | rad/s | yaw rate command
single | x_cmd             | m | position x command, the frame is decided by frame variable
single | y_cmd             | m | position y command, the frame is decided by frame variable
single | z_cmd             | m | position z command, the frame is decided by frame variable
int32 | lat_cmd             | degE7 | latitude command in global frame
int32 | lon_cmd             | degE7 | longitude command in global frame
single | alt_cmd             | m | altitude command in global frame
single | u_cmd             | m/s | velocity x command, the frame is decided by frame variable
single | v_cmd             | m/s | velocity y command, the frame is decided by frame variable
single | w_cmd             | m/s | velocity z command, the frame is decided by frame variable
single | ax_cmd             | m/s^2 | acceleration x command, the frame is decided by frame variable
single | ay_cmd             | m/s^2 | acceleration y command, the frame is decided by frame variable
single | az_cmd             | m/s^2 | acceleration z command, the frame is decided by frame variable
uint16 | throttle_cmd             | [1000 2000] | throttle command
uint8 | frame             | - | Coordinate Frame:<br>0:FRAME_GLOBAL_NED<br>1:FRAME_LOCAL_FRD<br>2:FRAME_BODY_FRD
uint8 | reserved             | - | reserved
uint32 | cmd_mask             | - | Type bit mask for auto command:<br>0: p_cmd valid<br>1: q_cmd valid<br>2: r_cmd valid<br>3: phi_cmd valid<br>4: theta_cmd valid<br>5: psi__cmd_valid<br>6: psi_rate_cmd_valid<br>7: x_cmd valid<br>8: y_cmd valid<br>9: z_cmd valid<br>10: lat_cmd valid<br>11: lon_cmd valid<br>12: alt_cmd valid<br>13: u_cmd valid<br>14: v_cmd valid<br>15: w_cmd valid<br>16: ax_cmd valid<br>17: ay_cmd valid<br>18: ax_cmd valid<br>19: throttle_cmd valid

### Mission_Data_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | output timestamp
uint16 | valid_items             | - | valid waypoint items in mission data
uint16 | reserved             | - | reserved
uint16 | seq             | - | sequence number for item within mission (indexed from 0). 
uint16 | command             | - | command id, as defined in [MAV_CMD]([Messages (common) · MAVLink Developer Guide](https://mavlink.io/en/messages/common.html#mav_commands)) 
uint8 | frame             | - | The coordinate system of the waypoint, as defined in [MAV_FRAME]([Mission Protocol · MAVLink Developer Guide](https://mavlink.io/en/services/mission.html#MAV_FRAME)) 
uint8 | current             | - | When downloading, whether the item is the current mission item. 
uint8 | autocontinue             | - | Autocontinue to next waypoint when the command completes. 
uint8 | mission_type             | - | [Mission_type]([Mission Protocol · MAVLink Developer Guide](https://mavlink.io/en/services/mission.html#mission_types)) 
single | param1             | - | param #1. 
single | param2             | - | param #2. 
single | param3             | - | param #3. 
single | param4             | - | param #4. 
int32 | x             | - | X coordinate (local frame) or latitude (global frame) for navigation commands (otherwise Param #5). 
int32 | y             | - | Y coordinate (local frame) or longitude (global frame) for navigation commands (otherwise Param #6). 
single | z             | - | Z coordinate (local frame) or altitude (global - relative or absolute, depending on frame) (otherwise Param #7). 

### FMS_Out_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | fms output timestamp
single | p_cmd            | rad/s       | roll rate command in body frame
single | q_cmd            | rad/s       | pitch rate command in body frame
single | r_cmd            | rad/s       | yaw rate command in body frame
single | phi_cmd          | rad         | roll command in body frame
single | theta_cmd        | rad         | pitch command in body frame
single | psi_rate_cmd     | rad/s       | yaw rate command in body frame
single | u_cmd            | m/s         | velocity x command in control frame
single | v_cmd            | m/s         | velocity y command in control frame
single | w_cmd            | m/s         | velocity z command in control frame
single | ax_cmd           | m/s^2       | acceleration x command in control frame
single | ay_cmd           | m/s^2       | acceleration y command in control frame
single | az_cmd           | m/s^2       | acceleration z command in control frame
uint32 | throttle_cmd     | [1000 2000] | throttle command
uint16[16] | actuator_cmd | [1000 2000] | actuator command
uint8 | status            | enum VehicleStatus | vehicle status:<br>0: None<br>1: Disarm<br>2: Standby<br>3: Arm 
uint8 | state             | enum VehicleState | vehicle state:<br>0: None<br>1: Disarm<br>2: Standby<br>3: Offboard<br>4: Mission<br>5: InvalidAutoMode<br>6: Hold<br>7: Acro<br>8: Stabilize<br>9: Altitude<br>10: Position<br>11: InvalidAssistMode<br>12: Manual<br>13: InvalidManualMode<br>14: InvalidArmMode<br>15: Land<br>16: Return<br>17: Takeoff
uint8 | ctrl_mode         | enum ControlMode | control mode:<br>0: None<br>1: Manual<br>2: Acro<br>3: Stabilize<br>4: ALTCTL<br>5: POSCTL
uint8  | mode             | enum PilotMode | pilot mode:<br>0: None<br>1: Manual<br>2: Acro<br>3: Stabilize<br>4: Altitude<br>5: Position<br>6: Mission<br>7: Offboard
uint8  | reset            | -           | reset controller
uint8  | wp_consume       | -           | consumed waypoints. if wp_consume > 0 the flight controller will update the mission data by removing consumed waypoints and adding new ones. 
uint8  | wp_current       | -           | indicate the current waypoint 
uint8  | reserved        | -           | -
single[4]  | home        | [m m m rad]  | home position [x y h yaw]
