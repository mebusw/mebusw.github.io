title: 解决在iOS App中集成支付宝时遇到的RSA密钥签名问题
date: 2014-12-06 15:05:23

tags:
	- ios
	- signature
	- alipay
categories:
	- programming
	- swift
---


最近在自己的iOS native App中集成支付宝功能，发现有即时到帐、手机网页、快捷支付，之间的区别说的不够清楚，接口及参数定义更是各不相同。对于Native App来说，选择快捷支付(wap)就对了。

说起集成文档及Demo，发现文档质量比起五年前没啥进步，一如既往的不知所云，甚至缺少某些关键步骤。而且Demo中的代码一直强调此代码不完全能直接使用，可是我想说，开发者看demo的意义就在于能够快速的解决问题，最好能一键集成，SDK的集成度可以做得更好。可是无论文档和代码都缺少版本控制，二者之间存在大量不一致现象，更不用说出错后的信息让人难以跟踪，跟当年Windows的出错信息有得一拼。

下面说说集成快捷支付中遇到的一些关键环节，特别是RSA加密的问题，因为快捷支付只支持RSA，不支持MD5方式。
<!-- more -->

* PID: 申请快捷支付服务后，会在商家服务网页上看到pid等信息
* RSA加密: 然后用openssl生成商户的公钥和私钥。私钥用于提交支付申请时加密，而公钥要在商家服务页面上提交，换取到支付宝的公钥。
	* 提交公钥的时候，"合作伙伴密钥"和"无线产品密钥管理"都要提交，内容一样。公钥字符串不要包括`-----Begin Public Key...`字样，去掉空格换行后组装成一条字符串。这一步如果出错，就会在支付跳转时出现"ALI64错误"，文档中丝毫没提！
	* 对于iOS来说，私钥要转为PKCS8格式，即下述代码的第三行，而Android的话则不需要第三行！在代码中使用时，同样要，去掉空格换行后组装成一条字符串。
	```
	openssl genrsa -out rsa_private_key.pem 1024
	openssl rsa -in rsa_private_key.pem -pubout -out rsa_public_key.pem
	openssl pkcs8 -topk8 -inform PEM -in rsa_private_key.pem -outform PEM -nocrypt
	```
* 链接库：按照文档所述，导入alipay.framework和alipay.bundle。此外，demo代码中的`Order.m`、`\openssl`、`Util`文件和目录都要导入，并在工程的build settings中修改Search Path，消除找不到头文件的编译错误。
* App之间跳转设置。按照在项目Info.plist中设置URL Types->URL Schemes，内容自定，比如"alisdkdemo"，后续提交订单时要带这个参数，以便能从支付宝跳回你的App.
* 对于快捷支付的接口，在进行参数字符串拼接时，每个参数都形为`title="abc"&sign_type="RSA"`，注意到双引号了吗？这和PC端或手机网页接口都是不同的。


``` swift
        let order = Order()
        order.partner = "<你的PID>"
        order.seller = "<你的注册邮箱>"
        order.tradeNO = "201412061500"
        order.productName = "巧克力"
        order.productDescription = "夹心"
        order.amount = "0.01"
        order.notifyURL = "http://127.0.0.1:8000/xxx"
        order.service = "mobile.securitypay.pay"
        order.paymentType = "1"
        order.inputCharset = "utf-8"
        order.itBPay = "30m"
        order.showUrl = "m.alipay.com"
        
        let privateKey = "<对于iOS App，要把生成的私钥转为PCKS8格式，比原始私钥字符串更长一些。字符串不要包括`-----Begin Public Key...`字样，去掉空格换行后组装成一条字符串。>"
        
        let orderSpec = order.description
        
        let signer = RSADataSigner(privateKey: privateKey)
        let signedString = signer.signString(orderSpec)
        
        let orderString = "\(orderSpec)&sign=\"\(signedString)\"&sign_type=\"RSA\""
        println(orderString)
        
        AlipaySDK.defaultService().payOrder(orderString, fromScheme: "alisdkdemo", callback: {resultDic in
            println("Alipay result = \(resultDic as Dictionary)")
        })

```