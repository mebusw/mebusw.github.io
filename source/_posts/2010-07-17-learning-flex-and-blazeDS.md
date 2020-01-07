title: Flex及BlazeDS的功能原理及配置实例
date: 2010-07-17 00:05:11
tags:
	- flex
	- blazeDS
categories:
	- programming
	- actionscript
---


BlazeDS Test Drive里面示例了主要的功能：
* HTTPService
* Web Services
* Remote-Object
* Messaging

其实现是基于一个叫flex.messaging.MessageBroker的Servlet Dispatcher。各种消息通道的配置都存在于WEB-INF/flex/services-config.xml及其包含的3个XML文件中。下面简单介绍这四种不同的功能。
 
<!--more-->

## HTTPService:
向服务器发送HTTP请求，典型用途是获取服务器端的XML内容。服务器端可以采用任何技术：JSP、Servlet、ASP、PHP等，只要在请求的URL里面指定即可。这是个异步请求，服务器会返回result或fault事件，分别对应成功和失败。可以把返回的结果直接绑定到DataGrid上。利用useProxy属性和crossdomain.xml可以跨域访问，这是解决沙箱效应的方法之一。
另一种方法就是不用HTTPService，而是写一个JS或PHP脚本，用Ajax(XMLHTTP Object) 去访问跨域URL。（跑题了）

示例：在proxy-config.xml配置HTTPService要请求的URL地址，在程序中用“destination”映射关系来访问该URL，如示例中的“catalog”。将J返回的XML内容绑定到<mx:DataGrid>控件上。

``` [xml] main.mxml  
    <mx:HTTPService id="srv" destination="catalog" useProxy="true"/>  
    <mx:DataGrid dataProvider="{srv.lastResult.catalog.product}" width="100%" height="100%"/>   
    <mx:Button label="Get Data" click="srv.send()"/>  
```

``` [xml] WEB-INF/flex/proxy-config.xml
<destination id="catalog">  
    <properties>  
            <url>/{context.root}/testdrive-httpservice/catalog.jsp</url>  
    </properties>  
</destination>  
```
 
## WebService ：
可以访问基于SOAP的web服务，其返回的对象自动被反序列化为ActionScript对象。数据绑定、沙箱问 题等与HTTPService相同。
示例：在proxy-config.xml配置WebService要请求的wsdl地址，在程序中用“destination”映射关系来访问该URL，如示例中的“ws-catalog”。将返回的对象绑定到<mx:DataGrid>控件上。
 
``` [xml] main.mxml  
    <mx:WebService id="srv" destination="ws-catalog" useProxy="true" showBusyCursor="true"/>  
      
    <mx:DataGrid dataProvider="{srv.getProducts.lastResult}" width="100%" height="100%">  
        <mx:columns>  
            <mx:DataGridColumn dataField="productId" headerText="Product Id"/>  
            <mx:DataGridColumn dataField="name" headerText="Name"/>  
            <mx:DataGridColumn dataField="price" headerText="Price"/>  
        </mx:columns>  
    </mx:DataGrid>  
      
    <mx:Button label="Get Data" click="srv.getProducts()"/>  
```

``` [xml] proxy-config.xml  
    <destination id="ws-catalog">  
        <properties>  
            <wsdl>http://livecycledata.org/services/ProductWS?wsdl</wsdl>  
            <soap>*</soap>  
        </properties>  
        <adapter ref="soap-proxy"/>  
    </destination>  
```
 
## Remote-Object：
让用户可以直接访问服务器上的对象的方法，要被访问的对象是一个JAVA类，和Servlet、JSP差不多，但是远程请求的这部分被隐藏起来了。返回的JAVA对象被自动反序列化为Actionscript对象，如果不加以指定类型，就成为一个动态对象。远程访问也是异步的，result或fault事件，分别对应成功和失败。
 
示例：在remoting-config.xml配置远程对象的类名，在程序中用“destination”映射关系来访问该类的示例，如示例中的“product”。将返回的java.util.ArrayList对象绑定到<mx:DataGrid>控件上。
 
``` [xml] main.mxml  
    <mx:RemoteObject id="srv" destination="product"/>  
      
    <mx:DataGrid dataProvider="{srv.getProducts.lastResult}" width="100%" height="100%"/>   
    <mx:Button label="Get Data" click="srv.getProducts()"/>     
```

``` [xml] remoting-config.xml  
    <destination id="product">  
        <properties>  
            <source>flex.samples.product.ProductService</source>  
        </properties>  
    </destination>  
``` 
 
``` [java]
package flex.samples.product;  
import java.util.ArrayList;  
public class ProductService {  
        public List getProducts() {  
           List list = new ArrayList();  
           list.add(new Product(100, "abc"));  
           return list;  
        }  
}  
public class Product implements Serializable {  
        private int productId;  
        private String name;  
        public Product(int productId, String name)   {...}  
    public int getProductId() {  
        return productId;  
    }  
    public void setProductId(int productId) {  
        this.productId = productId;  
    public String getName() {  
        return name;  
    }  
    public void setName(String name) {  
        this.name = name;  
    }  
}  
``` 
 
## Messaging
消息本身包含消息头和消息体，消息头的格式是固定的，消息体的内容可以随意写。接触过状态机、分布式嵌入式系统编程的会很容易理解。
消息服务允许Flex客户端可以发布(Publish)和订阅(Subscribe)消息，相应的，客户端的被称为生产者(Producer)和消费者(Consumer)，其实也就是发送和接受消息了。
当一个消息被发到已订阅的目的地(destination)时，就会触发一个客户端的message事件。发布的任何消息会发给所有订阅的人，即使是生产者自己。如果某一订阅方只想收到特定的消息，可以在订阅时使用selecor属性来指定过滤条件，只有符合条件的消息才会被服务器转发。如果想要服务器产生消息，可以产生一个flex.messaging.messages.AsyncMessage类的实例，用flex.messaging.MessageBroker发送出去。
 
示例：在服务器端发布消息。通过JSP或Servlet建立一个无限循环线程产生消息。在messaging-config.xml配置消息通道（channel），在程序中用“destination”映射关系来访问该通道，如示例中的“feed”。

``` [java] startfeed.jsp  
<%  
    try {  
        Feed feed = new Feed();  
        feed.start();  
        out.println("Feed Started");  
    } catch (Exception e) {  
        out.println("A problem occured while starting the feed: "+e.getMessage());  
    }  
%>  
``` 

``` [java] Feed.java  
import java.util.*;  
import flex.messaging.MessageBroker;  
import flex.messaging.messages.AsyncMessage;  
import flex.messaging.util.UUIDUtils;  
public class Feed {  
    private static FeedThread thread;  
    public Feed() {  
    }  
    public void start() {  
        if (thread == null) {  
            thread = new FeedThread();  
            thread.start();  
        }  
    }  
    public void stop() {  
        thread.running = false;  
        thread = null;  
    }  
    public static class FeedThread extends Thread {  
        public boolean running = true;  
        public void run() {  
            MessageBroker msgBroker = MessageBroker.getMessageBroker(null);  
            String clientID = UUIDUtils.createUUID();  
            Random random = new Random();  
            double initialValue = 35;  
            double currentValue = 35;  
            double maxChange = initialValue * 0.005;  
            while (running) {  
                double change = maxChange - random.nextDouble() * maxChange * 2;  
                double newValue = currentValue + change;  
                if (currentValue < initialValue + initialValue * 0.15  
                        && currentValue > initialValue - initialValue * 0.15) {  
                    currentValue = newValue;  
                } else {  
                    currentValue -= change;  
                }  
                AsyncMessage msg = new AsyncMessage();  
                msg.setDestination("feed");  
                msg.setClientId(clientID);  
                msg.setMessageId(UUIDUtils.createUUID());  
                msg.setTimestamp(System.currentTimeMillis());  
                msg.setBody(new Double(currentValue));  
                msgBroker.routeMessageToService(msg, null);  
                System.out.println("" + currentValue);  
                try {  
                    Thread.sleep(300);  
                } catch (InterruptedException e) {  
                }  
            }  
        }  
    }  
}  
```

``` [xml] WEB-INF/flex/messaging-config.xml  
   <destination id="feed">  
        <!-- Destination specific channel configuration can be defined if needed  
        <channels>  
            <channel ref="my-streaming-amf"/>  
        </channels>          
         -->  
   </destination>  
```
 
示例：客户端可以发布和订阅消息，在messaging-config.xml配置消息目的地，在程序中用“destination”映射关系来访问该类的示例，如示例中的“chat”。注意，通道及其端点(endpoint)真正的定义是在service-config.xml中，messaging-config.xml中仅仅是引用它们。
 
 
``` [xml] 
<mx:Producer id="producer" destination="chat"/>  
<mx:Consumer id="consumer" destination="chat" message="messageHandler(event.message)" selector="prop1 = 10" />  
<mx:Script>  
    <!--[CDATA[  
      
        import mx.messaging.messages.AsyncMessage;  
        import mx.messaging.messages.IMessage;  
          
        private function send():void  
        {  
            var message:IMessage = new AsyncMessage();  
            message.headers = new Array();    
                               message.headers["prop1"] = 10;  
                               message.body.chatMessage = msg.text;  
            producer.send(message);  
            msg.text = "";  
        }  
                          
        private function messageHandler(message:IMessage):void  
        {  
            log.text += message.body.chatMessage + "/n";      
        }  
          
    ]]-->  
</mx:Script>  
```

``` [xml] WEB-INF/flex/messaging-config.xml  
    <destination id="chat"/>  
```

``` [xml] WEB-INF/flex/service-config.xml  
        <channel-definition id="my-streaming-amf" class="mx.messaging.channels.StreamingAMFChannel">  
            <endpoint url="http://{server.name}:{server.port}/{context.root}/messagebroker/streamingamf" class="flex.messaging.endpoints.StreamingAMFEndpoint"/>  
        </channel-definition>  
```
 
可以看到，messaging包括两大类不同的消息通道。分别是streaming channel和polling channel，每一类又有一些扩展类型。
 
采用polling channel，可以配置为每隔一段时间就轮询一次，也可以配置让服务器在无数据时等待，直到有数据时才响应（长轮询）。轮询响应完成后后就结束连接。
 
采用streaming channel时，服务器的响应一直保持连接状态，这样服务器一次连接后，可以持续下发数据到客户端。由于HTTP连接不是双工的，所以一条AMF或HTTP通道实际上需要2条浏览器连接，分别用于上行和下行数据。这第二条连接仅在需要向服务器发送数据时才建立，之后立即释放。这种通道特别适合实时性高、客户端刷新的应用。比起轮询，这种模式有效减少了反复建立连接的开销。
 
 
IE和Firefox浏览器在每个session最大连接数上有些不同。如果因此导致streaming channel建立失败，则BlazeDS会自动使用messaging-config.xml配置的下一个连接。
 
实例：定义多个默认通道，作为备份。

``` [xml] WEB-INF/flex/messaging-config.xml  
<default-channels>  
        <channel ref="my-streaming-amf"/>  
        <channel ref="my-polling-amf"/>  
                <channel ref="per-client-qos-polling-amf"/>  
</default-channels>  
``` 
 
如果不在XML中配置消息通道，也可以在Actionscript脚本中动态赋值。

``` [xml] 
<mx:Script>  
    <!--[CDATA[  
        import mx.messaging.channels.StreamingAMFChannel;  
        import mx.messaging.ChannelSet;  
        import mx.messaging.channels.AMFChannel;  
        import mx.messaging.events.MessageEvent;  
        import mx.messaging.messages.AsyncMessage;  
        import mx.messaging.messages.IMessage;  
        private function initComp():void  
        {  
            var myStreamingAMF:AMFChannel = new StreamingAMFChannel("my-streaming-amf", "../messagebroker/streamingamf");  
            var myPollingAMF:AMFChannel = new AMFChannel("my-polling-amf", "../messagebroker/amfpolling");  
            myPollingAMF.pollingEnabled = true;  
            myPollingAMF.pollingInterval = 2000;  
            var channelSet:ChannelSet = new ChannelSet();  
            channelSet.addChannel(myStreamingAMF);  
            channelSet.addChannel(myPollingAMF);  
            consumer.channelSet = channelSet;                 
            producer.channelSet = channelSet;  
        }  
  
    ]]-->  
</mx:Script> 
``` 