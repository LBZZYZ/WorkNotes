# 简介
RRPC是指客户云端通过云端API发起一个RRPC调用，该调用将同步返回设备的响应。设备端会收到一个同步请求的 topic, 格式如 `/ext/rrpc/{messageId}/{rrpc_topic}` 或者 `/sys/{pk}/{dn}/rrpc/request/${msgId}`, 设备端接收到该消息后，进行处理，并将处理结果 publish 到 `/ext/rrpc/{messageId}/{rrpc_topic}` 或者 `/ext/rrpc/${msgId}/${topic}`。
$\color{yellow}{注意}$ : 设备端首先需要订阅对应的下行topic，才能收到云端的RRPC同步调用。
[什么是RRPC](https://help.aliyun.com/apsara/integrated-iot/v_2_0_0_20210812/iot/wq9r8h/what-is-rrpc.html)
[参数列表](https://help.aliyun.com/apsara/integrated-iot/v_2_0_0_20210812/iot/q7bvtc/rrpc.html?spm=a2c4g.0.10001.338)

# 在线API调试
[工具](https://next.api.aliyun.com/api/Iot/2018-01-20/RRpc)

# Android实现方式
[RRPC能力](https://help.aliyun.com/document_detail/164267.html?spm=a2c4g.96607.0.0.7152136djnrrap)

# 原理图
![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9555655261/p11774.png)