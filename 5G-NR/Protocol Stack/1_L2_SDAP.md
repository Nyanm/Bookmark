
## 功能

- 完成QoS Flow与数据无线承载(DRB)之间的映射，一个或多个QoS可以映射到一个DRB，在上行同一时间一个QoS只能映射到一个DRB
- 在上下行数据分组中标记QoS Flow ID

## QoS流

QoS Flow在NAS层是一个PDU Session内区分QoS的最小粒度，由QFI唯一标记。同时每个QoS Flow都有相关联的QoS属性集合，描述了其在UE与UPF之间传输时需要满足的性能要求（如比特率，时延，错包率等）。

核心网会为每个UE建立一个或多个PDU Session，每个PDU Session会话内会建立多个QoS Flow。同时，接入网根据不同QoS Flow的转发处理需求建立不同的DRB，或将属于同一PDU Session的多个QoS Flow映射到同一个DRB上。



