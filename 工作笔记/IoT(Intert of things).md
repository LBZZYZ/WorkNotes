```
\\INTELLYVA-SMB\share\temp\from vincy.wu\A-待确认内容\1209云服务后台
```

https://help.aliyun.com/document_detail/96607.html
https://help.aliyun.com/document_detail/261160.htm?spm=a2c4g.11186623.0.0.3f0a11ff9pcOQa#section-fct-pw1-b5p

![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/5826597261/p132750.png)

LightSwitch设备信息
{ "ProductKey": "ie42YsvOd8P", "DeviceName": "LightSwitch", "DeviceSecret": "88803e43e385ccbea2f1a9164f21379d" }

Topic类
/ie42YsvOd8P/${deviceName}/user/get
/ie42YsvOd8P/${deviceName}/user/update

```
${YourProductKey}.iot-as-mqtt.${YourRegionId}.aliyuncs.com:443
```

### 设备证书三元组
烧录方式
[1] [一机一密](https://help.aliyun.com/document_detail/74005.htm?spm=a2c4g.11186623.0.0.3f0a36e8XrnWae#task-n21-glp-wfb)
[2] [一型一密](https://help.aliyun.com/document_detail/74006.htm?spm=a2c4g.11186623.0.0.3f0a36e8XrnWae#task-m1l-zqq-wfb)
![](https://cdn.staticaly.com/gh/LBZZYZ/PicX@master/image.7jyvspo5w2o0.webp)

### 一型一密流程图
![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/6682925161/p133168.jpg)

### 基于Mqtt Topic通信
https://help.aliyun.com/document_detail/96610.html?spm=a2c4g.96607.0.0.7152136d2tWZwC

### 物联网平台与服务器数据流转
![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/8876549951/p87244.png)
IotInstanceId : iot-06z00iq4w7u55r0
product key : ie42YsvOd8P
product secret : 0CPWWO45NLtuv55r
device name : NewDevice
device secret : ae8a37c6a3a2b09d665e13948efef317
topic : /ie42YsvOd8P/NewDevice/user/get
Access Key id : LTAI5tRHJkHV39UerX8yrVpo
Access Key secret : m7eVw1noJ7HWMPJRRFfK7tjIxWA59Z

### AMQP客户端接入说明
https://help.aliyun.com/document_detail/142489.html


### 通信方式概述
https://help.aliyun.com/document_detail/146382.html?spm=a2c4g.89226.0.0.4bec5a1cUxBsru

### Topic定义
https://blog.csdn.net/LXDOS/article/details/123679872

topic(消息标题) + payload(消息内容) 这一组合可以构建出万能的结构，如何设计出良好的结构是难点
参考:https://wwj718.github.io/post/%E7%BC%96%E7%A8%8B/mqtt-topic-payload-design/
-   为单个设备或一小组设备使用相同的topic。
-   为data和commands使用单独的topic。
-   消息payload中的数据应该是特定于设备的。
-   消息payload中与设备的多个属性相关的数据是JSON编码的


### RRPC
https://help.aliyun.com/apsara/integrated-iot/v_2_0_0_20210812/iot/wq9r8h/what-is-rrpc.html
![](https://help-static-aliyun-doc.aliyuncs.com/assets/img/zh-CN/9555655261/p11774.png)

## 服务端开发人员提问文档
[链接](https://docs.qq.com/scenario/account-guide.html?globalPadId=300000000%24TgHolFEsDQlU&redirect_url=https%3A%2F%2Fdocs.qq.com%2Fdoc%2FDVGdIb2xGRXNEUWxV%3Fu%3Dac3ca48d84e64b61b05af80b9c077218&u=5e4efa64f2e74cf4a56a3f7adfff265b)
