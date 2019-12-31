title: 理解unicode和utf8字符编码
date: 2015-01-13 09:58:27
tags:
	- unicode
	- utf8
	- ascii
	- encoding
categories:
	- programming
	- python
---
![](http://a1.att.hudong.com/32/85/01300000194281121846859532172.jpg)

本文以python语言为例，解答下面几个问题：理解unicode的统一性，unicode与utf8和ASCII到底是什么关系？又如何在实践中用好字符编解码呢？

# 令人头疼的字符编解码乱象
由于历史原因，中文字符编码标准众多，特别是在Windows操作系统上，GBK,GB2312,UTF8等等，各不相同，还记得各种场合出现的乱码吗？而在Mac系统上，由于完全统一用UTF8编码，所以编程人员在开发调试时就不大会因为编码操心。然而，一旦涉及到与第三方系统对接，还是不免会遇到编解码的问题，不论这种多系统集成的模式叫做SOA或者MicroService也好，每个系统都有自己的接口定义，即使是用SOAP协议夜还会有不同版本，更不用说各种自定义的基于XML或JSON的准Restful协议了。
总之，不同操作系统，不同的第三方系统，带来了不同字符编解码复杂局面。

<!--more-->

# ASCII

1968年，美国发布了*American Standard Code for Information Interchange*用于对字符进行编码，是最著名的编码标准之一。那个时候的计算机大多是8位的，每个字节(byte)可以表示数字0~255，而ASCII只用到了整数0~127，包含了常用的数字、英文字母、符号和一些控制字符，却根本没有考虑其他国家的语言，例如拥有成千上万字符的汉字，甚至英语本身的重音字符，比如'naïve'。

同时，不同的编解码标准之间也是无法互相引用的。汉字有自己的GB2312，俄语用KOI8，法语用的是Latin1。

# Unicode

Unicode标准(ISO10646)为了统一全球编解码标准而提出，最初打算采用16位编码，即0~65535，但后来发现还是不够用，所以现在是用到了0~1,114,111 (0x10ffff)。
Unicode用十六进制整数来表示字符，称为码点(code point),比如`U+5bfc`就是用十六进制0x5bfc表示中文字符：`导`。

Unicode同时也兼容ASCII码，比如字符`P`的码点仍然是`0x50`，用32bit表示就是`0x50 00 00 00`，这就带来了很多空间浪费，而且不同处理器的字节处理顺序不同(大小字端问题)，而且C语言字符串函数和一些网络传输协议看到这么多0也会直接晕菜。
因此unicode是一种内部表示，人们不会直接用它来编码的，而是采用UTF-8。

# UTF-8

*Unicode Transformation Format 8-bit*是最常用的多语言编码方式。(相比之下，UTF-16就不常用)。其规则如下：
1. 如果码点小于128，则用ascii相应的单字节表示
2. 如果码点介于128和0x7ff之间, 则采用双字节表示，每个字节的值介于128~255
3. 如果码点大于0x7ff, 则采用三或四字节表示，每个字节的值介于128~255

下表以python为例，展示几个概念之间的区别：

要表达的中文字符 | unicode码点 | utf8编码 | gb2312编码 | ascii编码 
-|-|-|-|-
'abc'     | u'abc' | 'abc' | 'abc' | 'abc'
'abc申导xyz'     | u'abc\u7533\u5bfcxyz' | 'abc\xe7\x94\xb3\xe5\xaf\xbcxyz' | 'abc\xc9\xea\xb5\xbcxyz' | 无法编码


你可以在python console里面试验一下，我的操作系统是MacOS，所以console是utf8环境。
``` [py]
	u8='abc申导xyz'
	#>>> 'abc\xe7\x94\xb3\xe5\xaf\xbcxyz'
	un=u'abc申导xyz'
	#>>>  u'abc\u7533\u5bfcxyz'
	print type(u8)
	#>>> str
	print type(un)
	#>>> unicode

	print u8, un
	#>>> abc申导xyz abc申导xyz
	print un.encode('gb2312')
	#>>> abc?굼xyz    (意料之中的乱码，如果是在Windows上就不同了)
	print str(un)
	#>>> UnicodeEncodeError: 'ascii' codec can't encode characters in position 3-4: ordinal not in range(128)

	print u8 == un 
	#>>> UnicodeWarning: Unicode equal comparison failed to convert both arguments to Unicode - interpreting them as being unequal
	print un.encode('utf8') == u8
	#>>> True
	print u8.decode('utf8') == un
	#>>> True

```

# python编程中的最佳实践
（其他语言和平台可以借鉴）

在python中，`unicode`是一种内部数据类型，而一旦用于打印显示和外部传输时，必须要对其进行编码(encode)，转换成为某一种编码格式，如utf8/ascii/gb2312等，无论采用哪种编码，转换后的python数据类型都是`str`。同样，当你收到外界传来的字符串时，必须要知道其所用的编码格式，然后解码(decode)为`unicode`。

**当没有指定格式时，默认字符编解码器都是ascii codec！**

python默认编码为ascii。建议不论在什么操作系统上，对包含中文字符的py源文件，都在开头加上如下字符，即可强制编译器和IDE将字符识别为utf8编码。

	#!/usr/bin/env python
	# encoding: utf-8

无论你编码环境有多复杂，坚持使用`unicode`类型作为基础类型，以其为中心进行各种格式的转换。因为不同编码格式之间是不能直接进行字符串拼接、查找等操作的。

另外，建议使用自带单元测试工具pyunit进行编解码的测试，避免部署到服务器以后再通过抛出的编码异常来测试，可以有效提高效率。@申导 昨晚困扰到凌晨,是因为XML lib不能直接识别unicode，必须要显式地将字符串编码后，再进行XML解析。因为这个bug所在代码只能在外网环境被调用，不像本地可以用调试器跟踪断点，今晨通过单元测试终于定位到了这个bug。


参考：
<https://docs.python.org/2/howto/unicode.html>
