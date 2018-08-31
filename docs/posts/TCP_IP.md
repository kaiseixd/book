# 网络
## TCP/IP 协议分层模型

- 应用层
  - 网络应用程序及它们的应用层协议存在的地方
  - 报文（message）
  - 协议：HTTP、FTP

- 运输层：
  - 负责在两个（不同主机的）应用程序端点之间传递报文
  - 报文段（segment）
  - 协议：TCP、UDP

- 网络层：
  - 将数据报从一台主机传到另一台主机上（只负责提供源地址和目标地址的位置?）
  - 数据报（datagram）
  - 协议：IP

- 链路层
  - 将帧从当前节点移动到下一个节点（具体传输的过程是由它来负责?）
  - 帧（frame）

- 物理层：
  - WiFi、4G

## 应用层
### HTTP

Hyper Text Transfer Protocol（超文本传输协议）

## 运输层
### 端口

protocol，用于区分主机的不同进程（?），大小在 0-65535 之间

### UDP

User Datagram Protocol，不可靠传输

### TCP

Transmission Control Protocol，可靠传输

## 网络层
### IP

负责主机之间的通信，但是不保证数据能送达并且完整（不可靠服务）

### DNS

Domain Name System（域名系统），运行在 UDP 协议上，用于转换 ip 和域名

采用分布式的设计方案，如果本地的 DNS 服务器上找不到对应的地址的话就会再往上级传

### DHCP

Dynamic Host Configuration Protocol（动态主机配置协议）

建立过程：

1. 客户端通过广播发送 DHCP discover 消息，所有 DHCP 服务器都将接收这个消息
2. 服务器通过广播回应一个 DHCP offer 消息（以第一个为准）
3. 客户端通过广播发送 DHCP request 消息，包含申请的 ip 地址，并通知其他服务器不必响应
4. 服务器回应 DHCP ACK 消息

## 链路层

主机，路由器是通过一条条的物理链路链接在一起

### 地址

LAN 地址、物理地址、MAC 地址

### ARP

Address Resolution Protocol，将 ip 地址解析为 MAC 地址

## 物理层






