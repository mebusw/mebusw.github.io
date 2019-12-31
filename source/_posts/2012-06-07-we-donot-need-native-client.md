title: JavaScript创始人Brendan Eich：我们不需要Native Client
date: 2012-06-07 20:23:31
tags:
	- javascript
categories:
    - programming
    - javascript

---



[Native Client](https://developers.google.com/native-client/overview) 是Google支持的开源技术，用来在浏览器中像桌面程序一样运行编译过的代码，满足人们对web应用程序的可移植性和安全性的期望，提供更好的富客户端用户体验，允许开发者编写更强大的移动Web应用程序。
JavaScript创始人Brendan Eich上个月在旧金山召开的O'Reilly Fluent ConfereNative Cliente大会上解释说JavaScript足以满足Google对Native Client的设计目的，并[怀疑Native Client是否能够像JavaScript一样，得到浏览器厂商的广泛支持](http://m.infoworld.com/d/application-development/javascript-founder-dismisses-google-native-client-194467)。

<!--more-->

Eich认为JavaScript自17年前诞生起就能够在浏览器中安全运行可移植的本地代码，而且得到各大浏览器厂商如Apple、Microsoft、Mozilla的支持。而出品了Chrome浏览器的Google想要使Native Client获得同样的支持会比较困难，因其自己也是浏览器市场竞争者。
现任Mozilla的首席技术官Eich谈到JavaScript的可达性和内存安全等优点。或许是回应Native Client的“双沙箱”技术， Eich指出“JavaScript也能以沙箱运行，我们不需要Native Client”。 另外，Native Client支持C/C++程序员将代码编译为Native Client模块，从而获得类似JavaScript的可移植性。对此Eich引用Low Level JavaScript项目作为回应。该项目可以将代码编译为JavaScript，并提供类C语言类型系统的手动内存管理和内存安全特性。
Eich谈到下一个JavaScript官方升级版本- ECMAScript 6已经在Chrome和Spider Monkey引擎上揭开部分面纱，并将更好的支持应用程序、库和代码生成，他强调并不打算将JavaScript变成类似Java的东西。ECMAScript 6将包含字符串插值、代替undefined值的缺省值、对象索引等变化，并淘汰参数对象。Eich认为JavaScript将不断变化成为更好的编译目标语言，“有趣的是，人们以我所不能预见的方式在使用它”。
有人认为即使JavaScript V8引擎也要比Native Client慢上许多倍，JavaScript并不能胜任高性能移动应用，对此Eich认为与版本和CPU架构有关。 另外有人质疑JavaScript的创始人并不喜欢来自Native Cient的竞争，对此，Eich亲自现身称无论Native Client还是Dart语言，都很难赢得Chrome之外的浏览器支持。


原文：

<http://www.infoq.com/cn/news/2012/06/we-dont-need-native-client>
