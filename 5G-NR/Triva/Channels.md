
## 无线承载 Radio Bearer

无线承载分为数据无线承载(DRB, Data Radio Bearer)与信令无线承载(SRB, Signalling Radio Bearer)。DRB位于SDAP层与PDCP层之间，用于映射QoS Flow。在UPF与gNB的通信中将数据映射到QoS Flow，而SDAP层再将QoS Flow映射到DRB上。SRB位于RRC层与PDCP层之间，用于传输系统消息与RRC消息。

SRB又分为以下几部分：
- SRB0
	- 传输CCCH承载的RRC消息
	- 在随机接入过程中建立，UE在RRC_IDLE状态就可以获得SRB0的配置和资源，如果需要可以直接使用
- SRB1
	- 传输DCCH承载的RRC消息
	- 通过SRB0建立，SRB1建立后UE进入RRC_Connected状态（RRC建立过程可看作通过SRB0建立SRB1的过程）
	- 可用于发送大部分RRC信令，或通过Piggybacked的方式传输NAS信令
	- 建立并管理DRB用来传输用户数据
- SRB2
	- 传输DCCH承载的NAS消息
- SRB3
	- 在EN-DC场景下用DCCH逻辑信道承载特殊RRC消息

## 逻辑信道 Logic Channel

逻辑信道位于MAC层与RLC层之间，其只关注传输信息内容和类别，分为控制信道(Control Channel)和业务信道(Traffic Channel)

|Full Name|Acronym|Type|Description|
|---|---|---|---|
|Broadcast Control Channel|BCCH|Control|用于广播系统控制信息的下行信道|
|Pagging Control Channel|PCCH|Control|用于传输寻呼信息和系统信息变化通知的下行信道|
|Common Control Channel|CCCH|Control|用于在UE和网络之间还没有建立RRC连接时，发送控制信息|
|Dedicated Control Channel|DCCH|Control|用于在RRC连接建立之后，UE和网络之间发送一对一的专用控制信息|
|Dedicated Traffic Channel|DTCH|Traffic|专用于一个UE的点对点用户信息传输的信道，上下行链路中都有|

## 传输信道 Transmission Channel

传输信道位于MAC层与PHY层之间，定义了空口传输数据的方式与特征（如调制编码方式，交织方式，冗余校验等），根据传输内容不同分为共享信道与专用信道(DL-SCH, UL-SCH)

|Full Name|Acronym|Direction|Description|
|---|---|---|---|
|Broadcast Channel|BCH|Downlink|通过广播的方式传输下行控制信息|
|Downlink Shared Channel|DL-SCH|Downlink|用于传输下行控制或者用户信息|
|Paging Channel|PCH|Downlink|用于传输寻呼信息|
|Uplink Shared Channel|UL-SCH|Uplink|用于传输上行控制或者用户信息|
|Random Access Channel|RACH|Uplink|用于传输随机接入前导码|

## 物理信道 Physical Channel

物理信道是空口的承载媒体，对应真实存在的射频资源（如时隙，载波频率等）

|Full Name|Acronym|Direction|
|---|---|---|
|Physical Downlink Control Channel|PDCCH|Downlink|
|Physical Downlink Shared Channel|PDSCH|Downlink|
|Physical Broadcast Channel|PBCH|Downlink|
|Physical Uplink Control Channel|PUCCH|Uplink|
|Physical Uplink Shared Channel|PUSCH|Uplink|
|Physical Random-Access Channel|PRACH|Uplink|
