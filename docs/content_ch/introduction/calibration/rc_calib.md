
## 遥控校准

遥控校准目前无法通过QGC地面站进行校准，原因是QGC遵循PX4的校准逻辑，会将油门信号校准到[0 1]的区间，而这跟FMT的需求是相冲突的，FMT要求油门的信号校准到[-1 1]的区间。

为了解决这个问题，FMT内置了一个遥控校准指令`rc calib`。



- Step1：在校准之前，请先确保遥控接收机有连接到飞控并且遥控器有打开。我们可以输入`mcn list`指令来查看*rc_channels*的消息的发布频率是否为非0，非0则表示有正常收到遥控信号。

<img src="figures/rc_calib1.png" width="50%">

- Step2：输入`rc calib`指令，并按照提示进行遥控校准。

<img src="figures/rc_calib2.jpg" width="50%">

- Step3：校准完成后，校准参数将更新到`RC`参数组中，可以通过`param list RC`指令查看。`rc_trim_channels`消息是由原始遥控消息`rc_channels`校准后得到的，校准后的最大、最小和中值应该分别为2000，1000和1500。
- Step4：确认校准参数无误后，在控制台输入`param save`保存校准结果，否则系统断电将丢失未保存的校准结果。
