title: comet长连接
date: 2011-06-17 22:35:33
tags:
	- comet
categories:
    - programming
    - architecture
---


Comet长连接用于服务器消息推送。两种实现方式long poll和stream。和http 1.1并无直接联系。
 
* Long Poll：服务器在发送响应后保持socket一段时间后才关闭socket连接。CS双方都为对方的request或response设置timeout来探测对方是否还在正常工作，所以每次request还可以起到心跳消息的作用。
 
* Stream：服务器在发送响应后保持socket一段时间后才不关闭socket连接，而是不断写入数据，这样客户端可以持续的分段收到数据。
tomcat提供了特殊的CometServlet来实现此功能。
在浏览器端可以用个隐藏的iframe元素来接受数据，或者google提供的“htmlfile”小工具来接收javascript