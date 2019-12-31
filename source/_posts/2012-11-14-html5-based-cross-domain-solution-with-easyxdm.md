title: 基于HTML5的EasyXDM组件实现浏览器兼容的跨域资源请求
tags:
  - cross domain

categories:
    - programming
    - javascript

---

同源策略([http://baike.baidu.com/<wbr />view/1580195.htm](http://baike.baidu.com/view/1580195.htm))指浏览器客户端的javas<wbr />cript脚本，只被允许访问来自同一站点的资源，<wbr />阻止来自其它站点可能怀有恶意的资源。<wbr />这种策略在保证安全的同时，也带来了站点间交互的诸多限制。<wbr />一直以来，存在多种解决方案来实现跨域资源请求，比如基于fla<wbr />sh或基于iFrame等等，web开发者需要选择不同的方案来<wbr />兼容各种浏览器。

**HTML5 XDM**

近年不断发展的HTML5规范对XDM-跨文档消息传送机制进行<wbr />了规范，利用postMessage()（[http://<wbr />www.whatwg.org/specs/web-apps/<wbr />current-work/multipage/web-<wbr />messaging.html#dom-window-<wbr />postmessage](http://www.whatwg.org/specs/web-apps/current-work/multipage/web-messaging.html#dom-window-postmessage)）函数可以简单稳妥的实现对其他窗口及站<wbr />点的跨文档消息传送XDM(cross-document messaging)。

_postMessage(message, targetOrigin, transfer)_函数有三个参数。第一个message表示要<wbr />发送的消息，第二个参数表示消息接收方所在域。<wbr />第三个参数是可选的，用于渠道信息发送。

比如下面的例子简单演示了使用postMessage()向内嵌<wbr />iframe发送一条消息，并指定来源自[www.infoq.<wbr />com](http://www.infoq.com/)，防止其中文档来源被悄悄改变。
<div>

var iframWindow = document.getElementById("<wbr />theFrame").contentWindow;

iframWindow.postMessage("<wbr />private info", "[http://www.infoq.com](http://www.infoq.com/)");

</div>
&nbsp;

接收方[www.html5.org](http://www.html5.org/)收到XDM消息会异步触发wi<wbr />ndow对象的message事件。首先验证消息来源是已知的域[<wbr />www.infoq.com](http://www.infoq.com/)，在处理完消息后，<wbr />向发送方回复一条消息。
<div>

window.addEventListener(“<wbr />message”,function(event) {

if (event.origin == "[http://www.infoq.com](http://www.infoq.com/)") {

processMessage(event.data);

event.source.postMessage("<wbr />Heard You", "[http://www.html5.org](http://www.html5.org/)");

}

});

</div>
**easyXDM**

easyXDM ([https://github.com/<wbr />oyvindkinsey/easyXDM](https://github.com/oyvindkinsey/easyXDM))是一款轻量级开源j<wbr />avascript组件，很好的实现了各种跨域解决方案的封装，<wbr />来消除同源策略带来的限制，方便应用程序通过javascrip<wbr />t API进行跨域通讯。easyXDM借助传输栈允许两个窗口双向<wbr />交换基于字符串的消息，支持双向通讯、可靠性、<wbr />队列和发送方验证等。对于支持HTML5的浏览器如IE 8+, Opera 9+,Firefox 3+, Safari 4+, Chrome 2+等直接采用HTML5的postMessage()，<wbr />对于其他非HTML5浏览器，<wbr />则根据当前浏览器优先选择最有效的方法实现跨文档消息传送，<wbr />比如加载swf文件组件，创建iFrame等。

&nbsp;

easyXDM包含两个基本对象。

easyXDM.Socket对象包装了传输栈，<wbr />允许在通讯的消费者(客户端)和提供者(服务端)之间通讯，<wbr />并定义一系列属性用于异步事件。

服务端首先建立Socket：
<div>

**var** socket **= new** easyXDM.Socket({

onMessage**:function**(message, origin) {

_//do something with message_

}

});

</div>
客户端创建Socket并进行连接：
<div>

    **var** socket **= new** easyXDM.Socket({

remote**:** "[http://path.to/provider/](http://path.to/provider/)", _// the path to the provider_

onMessage**:function**(message, origin) {

_//do something with message_

}

});

socket.postMessage("hello world");

</div>
&nbsp;

easyXDM.Rpc对象允许创建带有代理对象进行远程方法调<wbr />用。Rpc与Socket使用相同的传输栈和配置属性。

在服务端配置被调用的远程对象：
<div>

**var** rpc **= new** easyXDM.Rpc({},

{

local**:** {

helloWorld**: function**(one, two, three, successFn, errorFn){

_// here we expose a simple method with three arguments_

_// that returns an object_

**return** {

this_is**:** "an object"

};

}

},

remote**:** {

helloWorld**:**{

_// here we tell the Rpc object to stub a method helloWorld for us_

}

}

});

</div>
在客户端可以进行基于JSON的远程方法调用：
<div>

rpc.helloWorld(1,2,3, **function**(response){

_// here we can do something with the return value from `helloWorld`_

}, **function**(errorObj){

_// here we can react to a possible error_

};

</div>
&nbsp;

**跨域资源请求**

在web开发中跨域资源请求相当常用。借助XDM，<wbr />可以将资源内容作为消息内容来传送。

下面举例如何使用easyXDM来跨站点文件请求：站点[clie<wbr />nt.com/index.html](http://client.com/index.html)获取[remote.com/<wbr />list.xml](http://remote.com/list.xml) 这种典型场景。首先在[remote.com](http://remote.com/)放置代理，<wbr />注意这个代理是纯HTML文件，例如[remote.com/<wbr />agent.html](http://remote.com/agent.html)

&nbsp;

在客户端[client.com/index.html](http://client.com/index.html)引用eas<wbr />yxdm.js后，添加以下代码，<wbr />发起远程连接并准备好回调函数来处理XML文件内容。

&nbsp;
<div>

var socket = new easyXDM.Socket({

remote: "[http://remote.com/agent.html](http://remote.com/agent.html)"<wbr />,

onMessage:function(message, origin) {

var data = $.parseXML(message);

//do something with message data

}

});

</div>
&nbsp;

在被请求的远程服务端[remote.com/agent.<wbr />html](http://remote.com/agent.html)，添加以下代码：list.xml的内容将被缓存并发送
<div>

$.ajax({

type: "GET",

url: “list.xml",

dataType: "text",

success: function (data) {

var socket = new easyXDM.Socket(

{

onReady: function(message, origin){ }

}

);

socket.postMessage('' + data);

}

});

</div>
&nbsp;

&nbsp;

小结：easyXDM开源组件封装和支持HTML5的postM<wbr />essage()和其他传统跨文档消息传送XDM方案，允许we<wbr />b开发人员可以方便安全的实现跨域资源请求，<wbr />并方便的兼容不同的浏览器。