### 封包列表

![image-20200221172055145](wireshark 的使用.assets/image-20200221172055145.png)

- No 列编号
- Time 列开启监控后到接收包的时间
- Source 源 IP
- Destination 目标 IP
- Protocal 协议
- Length 包长度
- Info 包信息

### 封包详细信息

- Frame： 物理层的数据帧概况
- Ethernet II： 数据链路层以太网帧头部信息
- Internet Protocol Version 4： 互联网层 IP 包头部信息
- Transmission Control Protocol：传输层的数据段头部信息
- Hypertext Transfer Protocol：应用层的信息

### 过滤器

- 捕捉过滤器：用于决定将什么样的信息记录在捕捉结果中，需要在捕捉前设置
  - https://wiki.wireshark.org/CaptureFilters
- 显示过滤器：在捕捉结果中进行详细查找，可以在得到捕捉结果后随意修改
  - https://wiki.wireshark.org/DisplayFilters