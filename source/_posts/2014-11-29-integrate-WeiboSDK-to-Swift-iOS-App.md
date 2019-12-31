title: 将新浪微博 WeiboSDK 桥接集成到用Swift语言编写的iOS App
date: 2014-11-29 15:20:35
tags:
	- swift
	- ios
	- weibo
	- sdk
	- xcode
	- objc
    - bridging

category:
	- programming
	- swift
---


昨日女神高圆圆嫁到了台湾去，各位屌丝粉丝哭晕在厕所。别说，昨夜还真梦到了女神。于是今日发奋图强，继续进行编程工作--集成微博SDK到我的Swift程序中，用于微博授权登录。
![](http://ww2.sinaimg.cn/bmiddle/3bb73a47jw1emrwn32ubtj20cj0m83zg.jpg)

前年曾经做过一次集成，那时还是ObjectiveC语言的天下，而今日Swift语言已成大势所趋。
由于iOS SDK是用ObjectiveC写成，在Swift中要用到语言桥接。


由于文档与代码脱节，自己摸索半天，还是遇到很多坑。特别是链接期直接抛异常出来，比如`'NSInvalidArgumentException', reason: '-[__NSDictionaryM weibosdk_WBSDKJSONString]: unrecognized selector sent to instance`，多是找不到链接库导致。

### 完整解决方案：
<!-- more -->

* 添加ObjC语言到Swift语言的桥接头文件(Bridging-Header)：新建任意ObjC文件，XCode就会提示并帮你创建这个文件。（如果要做单元测试，也要记得引用这个桥接文件）
* 将libWeiboSDK代码全部拷入你的工程里，然后在桥接头文件中引用：
	``` swift
	#import "libWeiBoSDK/WeiboSDK.h"
	```
* 在AppDelegate.swift中,更新代码如下：
	``` swift
	class AppDelegate: UIResponder, UIApplicationDelegate, WeiboSDKDelegate {
    func application(application: UIApplication, didFinishLaunchingWithOptions launchOptions: NSDictionary?) -> Bool {
    	// your other code...
        WeiboSDK.enableDebugMode(true)
        WeiboSDK.registerApp("你的移动端Appkey")
        return true
    }
    func application(application: UIApplication, openURL url: NSURL, sourceApplication: String, annotation: AnyObject?) -> Bool {
        return WeiboSDK.handleOpenURL(url, delegate: self)
    }
    func application(application: UIApplication, handleOpenURL url: NSURL) -> Bool {
        return WeiboSDK.handleOpenURL(url, delegate: self)
    }
    func didReceiveWeiboRequest(request: WBBaseRequest!) {
        if (request.isKindOfClass(WBProvideMessageForWeiboRequest)) {
            //TODO: sth
        }
    }
    func didReceiveWeiboResponse(response: WBBaseResponse!) {
        if (response.isKindOfClass(WBSendMessageToWeiboResponse)) {
            var message = "响应状态:\(response.statusCode.rawValue)\n响应UserInfo数据:\(response.userInfo)\n原请求UserInfo数据:\(response.requestUserInfo)"
            var alert = UIAlertView(title: "发送结果", message: message, delegate: nil, cancelButtonTitle: "确定")
            alert.show()
        } else if (response.isKindOfClass(WBAuthorizeResponse)) {
            var message = "响应状态: \(response.statusCode.rawValue)\nresponse.userId: \((response as WBAuthorizeResponse).userID)\nresponse.accessToken: \((response as WBAuthorizeResponse).accessToken)\n响应UserInfo数据: \(response.userInfo)\n原请求UserInfo数据: \(response.requestUserInfo)"
            var alert = UIAlertView(title: "认证结果", message: message, delegate: nil, cancelButtonTitle: "确定")
            alert.show()
        }
    }	
    ```
* 在发起微博授权的事件中，加入代码：
	``` swift
    func callWeibo() {
        println("weibo pressed")
        var request: WBAuthorizeRequest! = WBAuthorizeRequest.request() as WBAuthorizeRequest
        request.redirectURI = "https://api.weibo.com/oauth2/default.html"
        request.scope = "all"
        WeiboSDK.sendRequest(request)
    }	
    ```
* 各种framework库都要添加到工程中，可以参考Demo工程。
	* CoreGraphics
	* Foundation
	* UIKit
	* QuartzCore
	* ImageIO
	* CoreFoundation
	* AdSupport
	* SystemConfiguration
	* Security
	* libWeiboSDK
* 链接选项在target->build settings->linking->other linker flags中，添加`-all_load`
* 在`Info.plist`中添加`URL types`属性，在其下继续添加`URL Identifier`=`com.weibo`, `URL Schemes`=`wb<你的app key>`
* Swift语法不断更新中，可能会有个别语法和接口失效，没关系，安装Xcode的提示`fix-it`即可
* 在微博平台上及代码中将redirectURI设置为一致的，比如"https://api.weibo.com/oauth2/default.html"
* 你的apple developer program最好没过期



### 编译环境
XCode 6.1
[WeiboSDK 2.4.2](https://github.com/sinaweibosdk/)

### @申导 在Github上自问自答的问题单
<https://github.com/sinaweibosdk/weibo_ios_sdk/issues/51>