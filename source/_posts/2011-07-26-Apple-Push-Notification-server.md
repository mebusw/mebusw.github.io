title: 编写Apple Push Notification服务器
date: 2011-07-26 21:26:51
tags:
	- ios
	- APNS
	
categories:
    - programming
    - php
---


iPhone OS 3.0一个引入注目的新特性是[Push Notifications（推送通知）](http://developer.apple.com/iphone/program/sdk/apns.html)，它允许向已安装相关应用程序的各设备直接发送消息。苹果在新闻提示或IM应用中展示了此特性，它也十分完美地适合于我们的服务器监视服务程序Server Density。

![](http://hi.csdn.net/attachment/201107/26/0_13116709548hFU.gif)

我们的程序提供一个选项，当你设定的某个服务器事件发生时，通知会直接发送到你的iPhone上。这是非常有用的因为它提醒用户立即打开我们的程序查看引起此警示的服务器详情。

Apple提供了有关实现和处理设备上提示消息的 [iPhone OS 的详细代码文档](http://developer.apple.com/iphone/library/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/IPhoneOSClientImp/IPhoneOSClientImp.html) ，但它只包括消息提供者服务器端编程指南。

作为消息提供者，我们需要与 Apple推送通知服务 (APNS)连接以发送消息到iPhone。为减少电池使用，一个设备仅需维持与APNS的一个连接。

本教程将从代码的层面介绍关于怎样建立一个推送通知服务器以连接APNS并使用推送通知到我们的服务器监视iPhone程序上。我们是使用PHP进行开发的，我们的示例都是PHP 5兼容的。
  
### 基本结构

使用唯一的SSL许可证连接到APNS
循环通过你需要发送到消息
为各消息构建有效载荷
断开与 APNS的连接

`远程通知数据的流程是单向的。提供者将包括客户程序设备令牌和有效载荷的数据打包，发送到APNS，然后APNS再将通知发送给最终设备。`



![](http://hi.csdn.net/attachment/201107/26/0_13116709129eFe.gif)
  
### 限制
有效载荷限制为256字节 – 它包括了消息主体以及你希望传送带其他属性。推送通知并不适于传送大量的数据。例如，我们仅仅传送一条短消息通知服务器监视的事件已经被触发了。
APNS并不提供消息发送成功与否的回馈状态。一个原因是如果一个设备无法联系那么发送给它的消息将被存于队列中，然而只有最新发送的消息被存于队列中 – 覆盖了先前发送但不成功的消息。
推送通知不适合用于发送紧急通知，因为消息仅在设备具有wifi或手机服务连接的情况下才能被发送，这也是为什么我们推荐与其它方法如email或SMS一起使用的原因。
用来与APNS通讯的SSL许可证（下面将讨论）是在程序层生成的。本教程涉及到实现方法仅适于单个iPhone程序，所以如果你有多个程序，那么你需要修改代码使之适合于使用多个许可证。
  
### 设备令牌

每条推送消息都必须针对某特定设备。这是通过使用在你的iPhone程序中由APNS产生的唯一deviceToken（设备令牌）来实现的。一旦获取了此令牌，你需要将其存储于服务器而不是你的iPhone程序内。它看上去像这样：
c9d4c07c fbbc26d6 ef87a44d 53e16983 1096a5d5 fd825475 56659ddd f715defc

在我们的 Server Density iPhone 程序中，我们在程序启动时调用相应的令牌生成方法，然后通过 HTTP API 调用 传回给我们的服务器 。这将使得deviceToken存储于服务器的有关用户的数据库中，从而我们可以使用它与持有此设备的用户进行通讯。
  
### 反馈服务

Apple 还提供了一个 反馈服务 ，你应该定期查询。它提供了一个以前使用过但不再有效的（例如用户卸载了你的iPhone程序）设备令牌列表。你可以从你的数据库中删除这些设备令牌。

本教程不涉及反馈服务的使用。
  
### 许可证

要进行推送服务的第一件事就是获取推送许可证。它用来对你通过SSL与APNS通讯进行识别。

在Mac上生成 Apple推送通知SSL许可证：

1. 登录到 iPhone Developer Connection Portal 并点击 App IDs
2. 创建一个不使用通配符的 App ID 。通配符 ID 不能用于推送通知服务。例如，我们的iPhone程序ID像这样：  AB123346CD.com.serverdensity.iphone
3. 点击App ID旁的“Configure”，然后按下按钮生产 推送通知许可证。根据“向导”指导的步骤生成一个签名并上传，最后下载生成的许可证。此步骤在 Apple文档中 也有谈到。
4. 通过双击.cer文件将你的 aps_developer_identity.cer 引入Keychain中。
5. 在Mac上启动 Keychain助手，然后在login keychain中选择 Certificates分类。你将看到一个可扩展选项“Apple Development Push Services”
6. 扩展此选项然后右击“Apple Development Push Services” > Export “Apple Development Push Services ID123”。保存为 apns-dev-cert.p12 文件。
7. 扩展“Apple Development Push Services” 对“Private Key”做同样操作，保存为 apns-dev-key.p12 文件。
8. 需要通过终端命令将这些文件转换为PEM格式：``` [sh]
openssl pkcs12 -clcerts -nokeys -out apns-dev-cert.pem -in apns-dev-cert.p12
openssl pkcs12 -nocerts -out apns-dev-key.pem -in apns-dev-key.p12
```
9. 如果你想要移除密码，要么在导出/转换时不要设定或者执行：``` [sh]
openssl rsa -in apns-dev-key.pem -out apns-dev-key-noenc.pem```
10. 最后，你需要将键和许可文件合成为apns-dev.pem文件，此文件在连接到APNS时需要使用：
``` [sh]
cat apns-dev-cert.pem apns-dev-key-noenc.pem > apns-dev.pem```

将此文件保存为一个易记的名字，你有可能以后会用到它。上述步骤同样适合于生成产品许可证。
  
### 载荷内容

载荷 格式化为遵循 RFC 4627标准的JSON格式。它由以下几部分组成：
提示 – 显示于设备上的文本串
标识 – 设备屏幕中程序图标上显示的整数
声音 –  显示消息在设备的同时发出的声音的文本名字
本教程仅处理发送简单提示文本串，但也可以发送包括诸如显示自定义按钮等在内的各种选项的字典集。
  
### 创建载荷

使用 PHP 很容易根据数组并 转换成 JSON而创建载荷:
``` [php]
$payload['aps'] = array('alert' => 'This is the alert text', 'badge' => 1, 'sound' => 'default');
$payload = json_encode($payload);
```

显示 $payload 的内容可以看到传送到APNS 的 JSON字符串：
{
     "aps" : { "alert" : "This is the alert text", "badge" : 1, "sound" : "default" }
}

这将使消息显示于设备上，触发提升声音并将“1”置于程序图标上。默认按钮“Close”和“View”同时会显示于弹出窗口上。

对于 Server Density iPhone程序而言，让用户按下“View”直接进入产生此提示的服务器是很重要的，所以我们增加了额外的自定义值：
``` [php]
$payload['aps'] = array('alert' => 'This is the alert text', 'badge' => 1, 'sound' => 'default');
$payload['server'] = array('serverId' => $serverId, 'name' => $name);
$output = json_encode($payload);
```

当用户按下“View”后，自定义server值将被传递到设备中的程序。JSON 值如下：
{
     "aps" : { "alert" : "This is the alert text", "badge" : 1, "sound" : "default" },
     "server" : { "serverId" : 1, "name" : "Server name")
}

256字节的限制适用于整个载荷，包括自定义字典集。
  
### 原生接口

在Server Density中，一旦产生了一条提示，将建立一个载荷并插入队列中。因此有必要时我们可以同时发送多个载荷。

Apple推荐使用这种方法，因为如果你在发送各载荷时频繁连接和断开，APNS有可能会封锁你的IP。
如Apple 描述:
`原生接口使用原生socket，具有二进制内容，采用数据流技术，不产生回馈。`

  
![](http://hi.csdn.net/attachment/201107/26/0_1311671042qexQ.gif)

### 打开连接

打开连接的 PHP 5代码如下：
``` [php]
$apnsHost = 'gateway.sandbox.push.apple.com';
$apnsPort = 2195;
$apnsCert = 'apns-dev.pem';

$streamContext = stream_context_create();
stream_context_set_option($streamContext, 'ssl', 'local_cert', $apnsCert);

$apns = stream_socket_client('ssl://' . $apnsHost . ':' . $apnsPort, $error, $errorString, 2,
STREAM_CLIENT_CONNECT, $streamContext);
```

如果发送错误，你可以参考$errorString。它也包括了SSL许可证不正确时的详细信息。

许可证文件处于执行的PHP代码的当前工作目录下，如果需要你可指定其绝对路径。
注意测试时应该使用开发许可证及sandbox。成品主机名为 gateway.push.apple.com ，而且你必须使用不同的产品许可证。
  
### 发送载荷

在此，我们循环整个载荷队列进行发送。构建发送到APNS的二进制内容简单示例如下：
``` [php]
$apnsMessage = chr(0) . chr(0) . chr(32) . pack('H*', str_replace(' ', '', $deviceToken)) . chr(0) .
chr(strlen($payload)) . $payload;
fwrite($apns, $apnsMessage);
```

注意 $deviceToken 是从数据库中提取并去除空格得到的。我们还应该检查是否$payload超过256个字节。

$apnsMessage 包括了正确的二进制载荷，而fwrite 将载荷写入当前活动的数据流连接中。

完成后，应关闭连接：
``` [php]
socket_close($apns);
fclose($apns);
```
  
### php-apns

有一个开源服务器库[php-apns](http://code.google.com/p/php-apns/)实现了以上所有功能，它依赖于 memcached。我们不想使用任何第三方代码，所以完全自己编写了自己的服务器。我们使用自定义cron系统，几秒钟运行一次。
 
原文见：
[How to build an Apple Push Notification provider server (tutorial)]()

参考：
[Apple 文档](http://developer.apple.com/iphone/library/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html)