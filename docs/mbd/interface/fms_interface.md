## FMS Model Interface

### Input Interface

| Port Name   | Port ID | Bus Type        |
| ----------- | ------- | --------------- |
| Pilot_Cmd   | 1       | [Pilot_Cmd_Bus](#Pilot_Cmd_Bus)   |
| INS_Out     | 2       | [INS_Out_Bus](mbd/interface/ins_interface.md#INS_Out_Bus)     |
| Control_Out | 3       | [Control_Out_Bus](mbd/interface/controller_interface.md#Control_Out_Bus) |

### Output Interface

| Port Name | Port ID | Bus Type    |
| --------- | ------- | ----------- |
| FMS_Out   | 1       | [FMS_Out_Bus](#FMS_Out_Bus) |

### Pilot_Cmd_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | pilot_cmd output timestamp
single | stick_yaw        | [-1 1]      | stick value of yaw channel
single | stick_throttle   | [-1 1]      | stick value of throttle channel
single | stick_roll       | [-1 1]      | stick value of roll channel
single | stick_pitch      | [-1 1]      | stick value of pitch channel
uint32 | mode             | enum PilotMode | pilot mode:<br>0: None<br>1: Manual<br>2: Acro<br>3: Stabilize<br>4: Altitude<br>5: Position<br>6: Mission<br>7: Offboard
uint32 | cmd_1            | enum FMS_Cmd | fms command_1 (event command):<br>0: None<br>1000: CMD_PreArm<br>1001: CMD_Arm<br>1002: CMD_Disarm<br>1003: CMD_Takeoff<br>1004: CMD_Land<br>1005: CMD_Return<br>1006: CMD_Pause<br>1007: CMD_Continue
uint32 | cmd_2            | -           | fms command_2 (status command)

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
uint8  | wp_consume       | -           | consumed waypoints
uint8  | wp_current       | -           | current waypoint
uint8  | reserved1        | -           | -
