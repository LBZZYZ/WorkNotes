---
title: IM-数据协议设计
date: 2021-06-05 01:38:02
tags: Im
categories: Im项目
---

# 前言
本文主要介绍IM通讯软件的数据封装。我们知道，TCP是一种流式协议，即以字节流的形式在网络中传输。协议本身不存在边界，因此对于数据收发端，必须定义一种共识的协议，用于双方对数据进行编码与解码。一般来说，编码动作由发送端完成，解码则由接收端完成。 
本文介绍的数据封装形式表现为：协议头 + 协议体。下面首先介绍协议头的设计。

<!--More-->
---

# 协议头设计
首先考虑一个例子，客户端需要给服务器提供一个表单信息如下：
name : 如Bingzhi Li(变长字段)
年龄 : 22
电话 : 嵌入类型：手机 座机
语言 : 中文 英文
...

由于表单中存在很多变长字段，因此无法用结构体的形式对数据进行定长定义。

再考虑这样一个问题，客户端向服务器发送两个数据包：1.文字信息包（33字节）；2.登录信息包（22字节）。上文有提到，TCP是一种流式协议，如果在传输过程中发生“粘包”的现象，即客户端一次收到33+22=55字节的信息，这时如果没有定义双端的协议，则接收端根本没有办法将这55字节的协议拆分成两个包，这时就体现出了协议的重要性。
简单总结以下定义数据协议的原因：
1.保证消息完整性(在消息头定义包长度，解决TCP粘包问题)。
2.封装body使用的序列化协议可能不同，如protobuf,json,xml等。


![](header-1.png)
![](header-1-1.png)
![](header-2.png)
针对protobuf设计协议头，会有service id和cmd id两个字段，service id代表大的服务类，如登录，消息发送等，cmd id则代表具体的服务，如请求登录，请求离线，请求发送消息等。
为什么xml不需要这样定义？
因为它可以在收到消息之后再去解析，因为他是文本形式传输的，而protobuf为二进制，不便于解析。

nginx使用的协议：
![](header-3.png)

typedef struct
{
	uint8_t magic[2];         //magic number
	uint16_t version;         //protocol version
	uint16_t type;            //protocol type: json, xml, binary, protobuf and so on
	uint16_t len;             //body length
	uint32_t seq;             //message number
	uint16_t sid;             //service id
	uint16_t cid;             //command id
	uint8_t  reserve[2];      //reserve

}message_head_t;



