title: 命令行运行iphone模拟器以及单元测试
date: 2011-07-19 21:54:53
tags:
	- ci
	- ios
	- unittest

categories:
    - agile
    - engineering
---

由于需要搭建持续集成CI平台，因此需要利用命令行进行编译和单元测试。
命令行编译倒是容易，xcodebuild即可。但是如果运行Unit test甚至启动模拟器运行应用程序，成了国内外同行的大问题，苹果还没有来得及开放或者根本不愿意开放命令行，所以只好自己摸索。环境为XCode4.2。工程名为Krowdit.

参考：
<https://github.com/hborders/iphonesim/commit/27acdfdf6c900b0e9bbd061f45c904c1d18fd21d#commitcomment-484139>
<http://blog.carbonfive.com/2011/04/06/running-xcode-4-unit-tests-from-the-command-line/>
<http://longweekendmobile.com/2011/04/17/xcode4-running-application-tests-from-the-command-line-in-ios/>

关于使用Jenkins CI,参见[Jenkins CI 连接Mac 电脑失败](http://www.jackyshen.com/2011-07-20-fail-to-connect-Jenkins-to-Mac)

<!--more-->

先说在命令行中运行单元测试。
在Xcode4中，单元测试其实是在模拟器运行的，而不是mac电脑的命令行上。
因此在Xcode图形界面里执行Test命令后，可以看到编译结果的最后一行信息说：

	warning: Skipping tests; the iPhoneSimulator platform does not currently support application-hosted tests (TEST_HOST set).

即使你强行设置TEST_HOST也不行，因为苹果就是不让你用。

实际上，当编译结束时，xcodebuild已经在尝试通过执行${SYSTEM_DEVELOPER_DIR}/Tools/RunUnitTests 在本机命令行运行Unit Test， 该脚本又调用${SYSTEM_DEVELOPER_DIR}/Platforms/iPhoneSimulator.platform/Developer/Tools/RunPlatformUnitTests， 最后是${SYSTEM_DEVELOPER_DIR}/Tools/RunPlatformUnitTests.include

经过研究，上述的warning，只是苹果临时关闭了该功能，我们可以做些手脚来屏蔽它，让xcodebuild强制执行。
编辑`/Developer/Platforms/iPhoneSimulator.platform/Developer/Tools/RunPlatformUnitTests`文件：
找到95行，把

`Warning ${LINENO} "Skipping tests; the iPhoneSimulator platform does not currently support application-hosted tests (TEST_HOST set)."`


替换为

`export OTHER_TEST_FLAGS="-RegisterForSystemEvents"
RunTestsForApplication "${TEST_HOST}" "${TEST_BUNDLE_PATH}"`

大功告成!

然后你就可以进入工程所在的路径，在命令行里面编译加执行单元测试了

```
xcodebuild -alltargets -arch i386 -sdk iphonesimulator4.3 -configuration=Release
```

结果如下

```
	=== BUILD NATIVE TARGET Krowdit OF PROJECT Krowdit WITH THE DEFAULT CONFIGURATION (Release) ===
Check dependencies


=== BUILD NATIVE TARGET KrowditTests OF PROJECT Krowdit WITH THE DEFAULT CONFIGURATION (Release) ===
Check dependencies
......
Test Suite 'All tests' finished at 2011-07-19 06:05:12 +0000.
Executed 16 tests, with 0 failures (0 unexpected) in 0.008 (0.013) seconds


/Developer/Tools/RunPlatformUnitTests.include:334: note: Passed tests for architecture 'i386' (GC OFF)

/Developer/Tools/RunPlatformUnitTests.include:345: note: Completed tests for architectures 'i386'

Touch build/Release-iphonesimulator/KrowditTests.octest
    cd /tmp/jenkins/workspace/Krowdit-ios
    setenv PATH "/Developer/Platforms/iPhoneSimulator.platform/Developer/usr/bin:/Developer/usr/bin:/usr/bin:/bin:/usr/sbin:/sbin"
    /usr/bin/touch -c /tmp/jenkins/workspace/Krowdit-ios/build/Release-iphonesimulator/KrowditTests.octest


** BUILD SUCCEEDED **
```

提醒一下,记得把源代码文件(非单元测试代码)都加到Test Target中，否则在命令行编译时没有包括某些文件,会导致出ld链接错误(在Xcode图形界面中默认不需要这样做)

下面说运行从命令行中运行应用程序:
需要一个小工具`iphonesim`,从 <https://github.com/hborders/iphonesim/> 下载后编译成为一个可执行文件,用它来启动iphone模拟器.
由于模拟器不支持多个实例,所以运行前记得先把模拟器给关掉。按照以下命令来执行吧!
	
	iphonesim launch /Users/TadoSoft/Library/Application\ Support/iPhone\ Simulator/4.3.2/Applications/ED845558-5168-45C0-BA98-43AB3C238E9F/Krowdit.app/
