---
title: MQTT--普遍用于物联网、消息推送的轻量级消息传输协议
date: 2018-09-01 13:03:46
tags: mqtt
categories: mqtt
description: MQTT是一个基于客户端-服务器的消息发布/订阅传输协议。针对高延迟或不可靠的网络进行了优化，同时还确保了网络传输的可靠性。适合用于带宽小、内存小、处理能力有限、网络不可靠的环境。具有这些特点的MQTT很适合用于嵌入式设备，已普遍用于智能设备、医疗、智能汽车等领域。在Android、IOS等移动开发中，普遍作为消息推送的解决方案。
---

## MQTT是什么
MQTT是一个基于客户端-服务器的消息发布/订阅传输协议。针对高延迟或不可靠的网络进行了优化，同时还确保了网络传输的可靠性。适合用于带宽小、内存小、处理能力有限、网络不可靠的环境。具有这些特点的MQTT很适合用于嵌入式设备，已普遍用于智能设备、医疗、智能汽车等领域。在Android、IOS等移动开发中，普遍作为消息推送的解决方案。

## MQTT有谁在用

* __阿里巴巴__ ：阿里云提供的IOT解决方案：[https://www.aliyun.com/aliware/scenarios/case1](https://www.aliyun.com/aliware/scenarios/case1)
* __Facebook Messenger__ ：Facebook出品的聊天工具
* __EMQ__ ：百万级分布式开源物联网MQTT消息服务器：[http://www.emqtt.com/](http://www.emqtt.com/)
* __云智易__ ：云智易的设备是基于MQTT协议传输数据从而实现设备数据的上报与控制
* __网易云信__ ：其核心也是MQTT来实现消息推送的

还有更多的使用例子：[https://github.com/mqtt/mqtt.github.io/wiki/Example-Uses](https://github.com/mqtt/mqtt.github.io/wiki/Example-Uses)

## MQTT是怎么工作的
MQTT采用的是发布/订阅的方式通信，客户端（Client）发布消息，经由代理（Broker）转发给订阅者（也是客户端）。

![发布/订阅模式](https://cdn.nlark.com/yuque/0/2018/png/164396/1535701989397-7972015f-ed6d-4184-8d8d-69926e743f72.png "")

> 村民：狼来了，就喊救命，我来赶跑它。
> 小男孩：知道了
> 狼来了。
> 小男孩：狼来了！狼来了！救命啊！
> 村民过来把狼赶跑了。

订阅者（村民）通过主题（狼来了）向代理（小男孩）订阅消息，当发布者（狼）发布该主题的消息（狼来了）时，代理就将消息转发给此消息主题的订阅者（小男孩通知村民），订阅者就可以做出响应（把狼赶跑了）。订阅者和发布者都属于客户端，可以是一个设备或者一个应用程序。每个客户端都拥有唯一的ClientID。

MQTT底层采用的是TCP/IP实现，提供三种服务质量（QoS）：
* __至多一次__ ：消息发布成功与否完全依赖底层网络，消息可能会丢失。适用于要求不高的数据。
* __至少一次__ ：消息会确保到达，但是可能会重复。
* __只有一次__ ：消息确保到达一次，可用于数据要求比较高的环境，如计费系统。

## MQTT的一些术语
__客户端 Client__
使用MQTT的程序或设备。客户端总是通过网络连接到服务端。它可以

* 发布应用消息给其它相关的客户端。
* 订阅以请求接受相关的应用消息。
* 取消订阅以移除接受应用消息的请求。
* 从服务端断开连接。

__服务端 Server__
一个程序或设备，作为发送消息的客户端和请求订阅的客户端之间的中介。服务端

* 接受来自客户端的网络连接。
* 接受客户端发布的应用消息。
* 处理客户端的订阅和取消订阅请求。
* 转发应用消息给符合条件的已订阅客户端。

__订阅 Subscription__
订阅包含一个主题过滤器（Topic Filter）和一个最大的服务质量（QoS）等级。订阅与单个会话（Session）关联。会话可以包含多于一个的订阅。会话的每个订阅都有一个不同的主题过滤器。

__主题名 Topic Name__
附加在应用消息上的一个标签，服务端已知且与订阅匹配。服务端发送应用消息的一个副本给每一个匹配的客户端订阅。

__主题过滤器 Topic Filter__
订阅中包含的一个表达式，用于表示相关的一个或多个主题。主题过滤器可以使用通配符。

__会话 Session__
客户端和服务端之间的状态交互。一些会话持续时长与网络连接一样，另一些可以在客户端和服务端的多个连续网络连接间扩展。

__控制报文 MQTT Control Packet__
通过网络连接发送的信息数据包。MQTT规范定义了十四种不同类型的控制报文，其中一个（PUBLISH报文）用于传输应用消息。

## MQTT的怎么用？
首先，你要有个服务器，然后在服务器上部署MQTT环境，具体方法这里就不展开讲了。
只想试试就不用自己部署服务器了，有很多服务器提供免费测试使用，[public\_brokers](https://github.com/mqtt/mqtt.github.io/wiki/public_brokers)。
示例Android工程在这里：[https://github.com/bytehala/android-mqtt-quickstart](https://github.com/bytehala/android-mqtt-quickstart)
MQTT协议中定义了一些方法（也被称为动作），来于表示对确定资源所进行操作。这个资源可以代表预先存在的数据或动态生成数据，这取决于服务器的实现。通常来说，资源指服务器上的文件或输出。主要方法有：
* Connect。等待与服务器建立连接。
* Disconnect。等待MQTT客户端完成所做的工作，并与服务器断开TCP/IP会话。
* Subscribe。等待完成订阅。
* UnSubscribe。等待服务器取消客户端的一个或多个topics订阅。
* Publish。MQTT客户端发送消息请求，发送完成后返回应用程序线程。

## 参考资料

[What is mqtt？](https://www.ibm.com/developerworks/mydeveloperworks/blogs/aimsupport/entry/what_is_mqtt_and_how_does_it_work_with_websphere_mq?lang=en)

[MQTT wiki](https://github.com/mqtt/mqtt.github.io/wiki)

[MQTT协议中文版](https://mcxiaoke.gitbooks.io/mqtt-cn/content/mqtt/01-Introduction.html)
