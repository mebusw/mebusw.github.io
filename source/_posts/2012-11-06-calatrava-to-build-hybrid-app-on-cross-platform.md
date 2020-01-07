title: Calatrava：自由构建UI的跨平台移动框架
tags:
  - hybrid
  - mobile

categories:
    - programming
    - javascript
---


移动是未来计算的趋势，越来越多的人使用移动设备来访问互联网。但是目前至少三大平台：iOS、Android、移动Web。相比桌面Web，移动用户需要更好的体验、界面和设计。然而移动设备受限于电池、不可靠的网络连接和小尺寸屏幕。


几个月前，Martin Fowler写过一篇关于开发跨设备移动应用的[文章](http://martinfowler.com/articles/multiMobile)。他指出，要获得最佳用户体验，最直接的办法就是针对每个移动平台开发一个本地应用，但这却将带来极大的成本。或者选用跨平台工具箱，“一次编写随处运行”？然而没有几个产品真正获得成功。Web应用程序倒是可以以较低成本运行在跨平台之上，但是用户界面又受到限制。因此，不可避免地要在用户体验和成本之间做出选择。

或者，折中的办法就是开发混合式(hybrid)应用程序：结合Web和原生应用。

ThoughtWorks 探索这条路并取得一些进展，最近发布了[Calatrava开源框架](http://overwatering.org/blog/2012/10/announcing-calatrava/)。Calatrava的思路是开发者用跨平台的JavaScript编写客户端逻辑，这部分代码完全相同，运行在iOS、Android、移动Web的JavaScript解释器中。Calatrava提供本地桥接，允许逻辑来驱动原生UI。

![](http://www.infoq.com/resource/articles/calatrava/zh/resources/1.png)

它将移动应用程序分成两部分来考虑：UI和客户端逻辑主体(headless-body)。开发者使用JavaScript代码开发通用的控制器逻辑，而原生代码处理UI部分生成原生外观（native veneer），可移植的JavaScript代码和原生代码之间能够互操作，进而开发出混合移动应用。

Calatrava本身并不提供任何UI框架或建立UI抽象层。在iOS上，就用Objective-C和Cocoa Touch框架来构建UI；在Android上，就用Java和Android库；在移动Web上，就用HTML5、CSS3和你喜欢的JavaScript库。跨平台逻辑层与代表着应用表现层Page对象进行交互，该对象提供了从显示和交互机制中分离出的API接口。很多时候，对于移动应用来说，HTML5 UI已经足够好，所以Calatrava也允许在iOS和Android上桥接HTML5，但是应用绝不会绑定在HTML UI上。

这样，每个平台都有具有了自己独有的UI设计，避免了“恐怖谷效应”（uncanny valley effect）。应用开发可以从HTML UI开始，当你觉得某些地方不够好的时候，就用原生UI替换掉，且只对部分平台进行替换，而不影响其他部分。同时你也可以享受某些平台特有的UI特性。

Calatrava适合包含较多复杂的客户端逻辑的应用，且当应用偏重于成为现有产品的新渠道(Channel)，而非产品本身。如果应用的大部分代码是UI部分（如游戏），或是已经提供非常好的用户体验的Web应用，Calatrava就不太适合了。 Calatrava编写的核心JavaScript逻辑支持使用[Jasmine](http://pivotal.github.com/jasmine/)进行单元测试，以及使用[cucumer.js](https://github.com/cucumber/cucumber-js)进行功能测试。

想试试吗？从github的[Caltrava主页](http://github.com/calatrava/calatrava)可以找到说明，简单来说就是下面几步：

1.  安装依赖：Node.js, Ruby, Xcode, Android.
2.  安装Ruby插件gem install calatrava
3.  使用Calatrava工具创建项目calatrava create sample
4.  编译并运行项目
Calatrava还处在早期开发阶段，估计还是会有很多bug，另外插件、模块和文档也需要完善。

Martin Fowler分析了用户体验和可负担能力之间的动态平衡，他认为混合式（hybrid）解决方案介于纯本地应用和纯Web应用之间，更适合增量式发布。即首次发布（release）采用纯Web UI，之后的发布（release）逐步将Web UI转为本地UI特性，或逐步地增加本地UI特性的比例。

![](http://www.infoq.com/resource/articles/calatrava/zh/resources/2.png)

MartinFowler认为Caltrava最有价值的地方就在于它适合[增量式发布策略](http://martinfowler.com/snips/201210151007.html)。比如[cover-your-bases策略](http://martinfowler.com/articles/mobileImplStrategy.html#cover-your-bases)，即当你已经有大量用户基础，而移动应用定位为现有产品的新渠道(channel)。由于现有的用户，最重要的事情是将新渠道尽快地推到尽可能多的用户面前。很明显，平台覆盖率最重要。然而在移动平台上，体验也非常重要，所以应该用最小功能集提供简化的体验，而不是提供退化的体验。

![](http://www.infoq.com/resource/articles/calatrava/zh/resources/1.png)

它将移动应用程序分成两部分来考虑：UI和客户端逻辑主体(headless-body)。开发者使用JavaScript代码开发通用的控制器逻辑，而原生代码处理UI部分生成原生外观（native veneer），可移植的JavaScript代码和原生代码之间能够互操作，进而开发出混合移动应用。

Calatrava本身并不提供任何UI框架或建立UI抽象层。在iOS上，就用Objective-C和Cocoa Touch框架来构建UI；在Android上，就用Java和Android库；在移动Web上，就用HTML5、CSS3和你喜欢的JavaScript库。跨平台逻辑层与代表着应用表现层Page对象进行交互，该对象提供了从显示和交互机制中分离出的API接口。很多时候，对于移动应用来说，HTML5 UI已经足够好，所以Calatrava也允许在iOS和Android上桥接HTML5，但是应用绝不会绑定在HTML UI上。

这样，每个平台都有具有了自己独有的UI设计，避免了“恐怖谷效应”（uncanny valley effect）。应用开发可以从HTML UI开始，当你觉得某些地方不够好的时候，就用原生UI替换掉，且只对部分平台进行替换，而不影响其他部分。同时你也可以享受某些平台特有的UI特性。

Calatrava适合包含较多复杂的客户端逻辑的应用，且当应用偏重于成为现有产品的新渠道(Channel)，而非产品本身。如果应用的大部分代码是UI部分（如游戏），或是已经提供非常好的用户体验的Web应用，Calatrava就不太适合了。 Calatrava编写的核心JavaScript逻辑支持使用[Jasmine](http://pivotal.github.com/jasmine/)进行单元测试，以及使用[cucumer.js](https://github.com/cucumber/cucumber-js)进行功能测试。

想试试吗？从github的[Caltrava主页](http://github.com/calatrava/calatrava)可以找到说明，简单来说就是下面几步：

1.  安装依赖：Node.js, Ruby, Xcode, Android.
2.  安装Ruby插件gem install calatrava
3.  使用Calatrava工具创建项目calatrava create sample
4.  编译并运行项目
Calatrava还处在早期开发阶段，估计还是会有很多bug，另外插件、模块和文档也需要完善。

Martin Fowler分析了用户体验和可负担能力之间的动态平衡，他认为混合式（hybrid）解决方案介于纯本地应用和纯Web应用之间，更适合增量式发布。即首次发布（release）采用纯Web UI，之后的发布（release）逐步将Web UI转为本地UI特性，或逐步地增加本地UI特性的比例。

![](http://www.infoq.com/resource/articles/calatrava/zh/resources/2.png)

MartinFowler认为Caltrava最有价值的地方就在于它适合[增量式发布策略](http://martinfowler.com/snips/201210151007.html)。比如[cover-your-bases策略](http://martinfowler.com/articles/mobileImplStrategy.html#cover-your-bases)，即当你已经有大量用户基础，而移动应用定位为现有产品的新渠道(channel)。由于现有的用户，最重要的事情是将新渠道尽快地推到尽可能多的用户面前。很明显，平台覆盖率最重要。然而在移动平台上，体验也非常重要，所以应该用最小功能集提供简化的体验，而不是提供退化的体验。

原文：[http://www.infoq.com/cn/articles/calatrava](http://www.infoq.com/cn/articles/calatrava)
