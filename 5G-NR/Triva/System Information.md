
系统消息是小区级别的消息，用于为小区内的UE提供信息指导如何接入小区并正常工作。

从传输的角度，系统消息通过BCCH逻辑信道传输，其中MIB被映射到传输信道BCH，最后通过物理信道PBCH进行广播；而SIB被映射到传输信道DL-SCH，最后通过物理信道PDSCH进行传输。

从功能的角度，系统信息分为最小SI(Minimum SI)和其他SI(Other SI)，其中最小SI包含MIB和SIB，内容为初始接入所必须的信息及获取其他SI所必要的信息，会被周期广播（SIB也可以通过专用的方式进行发送），且UE必须先获取一个小区的最小SI内容才能驻留在该小区上；其他SI为MSI的补集，可以通过周期广播、按需广播或专用的形式提供给UE。

## 类型

- MIB (Master Information Block) 包含小区禁止状态信息和获取其他系统信息（如SIB1）所必须的物理层信息，以80ms为周期在BCH上广播
- SIB1 (System Information Block) 又被称为RMSI(Remaining MSI)包含其它系统信息的调度信息和初始接入所需要的信息（即其余SIB都通过SIB进行配置），以160ms的周期在DL-SCH上广播，或是以专用的方式发往RRC_CONNECTED的UE
- SIB2包含服务小区的小区重选相关信息
- SIB3~SIB5包含小区重选相关参数
- SIB6~SIB8包含ETWS通知和CMAS警告消息
- SIB9包含GPS时间和UTC时间相关信息