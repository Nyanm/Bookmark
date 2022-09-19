<!--p+p太好听了-->
<!--dreamin'太好听了-->

5G协议栈与4G协议栈相仿，垂直方向由控制面Control Plane和用户面User Plane组成：
- 控制面负责处理系统信令数据，与核心网控制面节点AMF(Access and Mobility Management Function)连接
- 用户面处理用户数据，与核心网用户面节点UPF(User Plane Function)连接

水平方向上，控制面协议栈由PHY, L2(MAC, RLC, PDCP), RRC, NAS组成；用户面协议栈在4G的基础上新增了用于映射QoS相关功能的层级，由PHY, L2(MAC, RLC, PDCP), SDAP组成

核心网协议栈分为物理层、数据链路层、IP层组成下三层；用户面的传输层选用IETF定义的UDP，而控制面的传输层选用IETF定义的SCTP。

无线接入网协议栈与传统的OSI协议栈相比：
- 接入网无网络层和传输层
- 控制面的RRC与NAS本质上属于应用层协议
- 无线传输物理信道条件更复杂
	- PDCP子层实现数据的加密与完整性保护来防止信息泄露与被篡改的风险
	- SDAP子层实现核心网QoS Flow到无线承载(Bearer)的映射和管理
	- L2的MAC子层与RLC子层间采用重传机制(ARQ, HARQ)保证传输的可靠性，抵抗无线信道的复杂影响造成的传输失败
	- MAC子层设置复用和优先级管理的功能以保证多用户QoS和公平性
	- MAC子层添加自适应编码，RLC子层添加对高层数据分段、~~级联~~功能应对物理层动态的实际传输能力
	- DTX/DRX功能下沉到MAC子层降低时延


