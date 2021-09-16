## Controller 模型接口

### 输入接口

| Port Name | Port ID | Bus Type    |
| --------- | ------- | ----------- |
| FMS_Out   | 1       | [FMS_Out_Bus](content_ch/mbd/interface/fms_interface.md#FMS_Out_Bus) |
| INS_Out   | 2       | [INS_Out_Bus](content_ch/mbd/interface/ins_interface.md#INS_Out_Bus) |

### 输出接口

| Port Name   | Port ID | Bus Type        |
| ----------- | ------- | --------------- |
| Control_Out | 1       | [Control_Out_Bus](#Control_Out_Bus) |

### Control_Out_Bus

Type   | Name             | Unit        | Comments
-----  | --------------   | ----------  | ----------------
uint32 | timestamp        | ms          | control output timestamp
uint16[16] | actuator_cmd          | [1000 2000] | actuator commands
