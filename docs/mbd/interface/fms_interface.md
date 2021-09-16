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
uint32 | timestamp        | ms          | rc timestamp
single | ls_lr            | [-1 1]      | left stick left/right
single | ls_ud            | [-1 1]      | left stick up/down
single | rs_lr            | [-1 1]      | right stick left/right
single | rs_ud            | [-1 1]      | right stick up/down
uint32 | mode             | -           | rc mode:<br>0: Unknown<br>1: Mission<br>2: Position<br>3: Altitude<br>4: Stabilize<br>5: Acro 
uint32 | cmd_1            | -           | rc event command
uint32 | cmd_2            | -           | rc statut command

### FMS_Out_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | fms output timestamp
single | p_cmd            | rad/s       | roll rate command, <br />valid on Acro Mode
single | q_cmd            | rad/s       | pitch rate command, <br />valid on Acro Mode
single | r_cmd            | rad/s       | yaw rate command, <br />valid on Acro Mode
single | phi_cmd          | rad         | roll command, <br />valid on Altitude, Stabilize Mode
single | theta_cmd        | rad         | pitch command, <br />valid on Altitude, Stabilize Mode
single | psi_rate_cmd     | rad/s       | yaw rate command, <br />valid on Mission, Position, Altitude, Stabilize Mode
single | u_cmd            | m/s         | velocity x command in control frame, <br />valid on Mission, Position Mode
single | v_cmd            | m/s         | velocity y command in control frame, <br />valid on Mission, Position Mode
single | w_cmd            | m/s         | velocity z command in control frame, <br />valid on Mission, Position, Altitude Mode
uint32 | throttle_cmd     | [1000 2000] | throttle command, <br />valid on Stabilize, Acro Mode
uint16[16] | actuator_cmd | [1000 2000] | actuator command, <br />valid on Disarm, Standby State
uint8 | state             | -           | vehicle state:<br />0: Disarm<br />1: Standby<br />2: Arm 
uint8 | mode              | -           | control mode:<br>0: Unknown<br>1: Mission<br>2: Position<br>3: Altitude<br>4: Stabilize<br>5: Acro 
uint8  | reset            | -           | controller reset
uint8  | reserved         | -           | -                                                            
