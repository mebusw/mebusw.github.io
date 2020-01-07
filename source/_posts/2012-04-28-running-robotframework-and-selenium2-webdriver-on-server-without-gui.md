title: 无GUI的server上通过虚拟显示来运行RobotFramework+Selenium2 (webdriver)
tags:
  - gui
  - robotframework
  - selenium
  - webdriver
  - xvfb
categories:
    - agile
    - engineering
---

RobotFramework是强大的开源自动化测试框架，与selenium2（webdriver）结合，可以自动化对网页应用进行测试，甚至A-TDD。

selenium需要打开浏览器，而浏览器需要图形化界面。如果你在liunx服务器上直接运行firefox，你会得到：

```
# firefox
Error: no display specified
```

如何在这种限制下进行Robot测试呢？

Xvfb是流行的虚拟现实库，可以使很多需要图形界面的程序虚拟运行。

`pyvirtualdisplay`是该库的python封装，进一步我们可以将其封装在Keywords中。那么你的Robotcase可能看起来是这样：

~ | ~ | ~
---- | ----- | ------ 
Run Keyword If | '${IsVirtualDisplay}'=='NO' | Open Virtual Display 
Open Browser | http://someurl | firefox 
Some Operations... ||
Close All Browsers ||
Run Keyword If | '${IsVirtualDisplay}'=='NO' | Close Virtual Display 


在本地具有GUI的机器进行Test case调试时，可以关闭该开关，而在CI服务器配置中，可以设置开关来打开虚拟显示。

``` bash
pybot -v IsVirtualDisplay:YES example.tsv
```

![](http://img.my.csdn.net/uploads/201212/06/1354783620_5063.png)


另外，Selenium2Library是在GitHub上的开源项目，很好将selenium2（webdriver）封装成Robot Keyword，但是往往这些还不够用。
肯定需要实现产品特性专用的Keyword Library。
如果单独写一个库，会无法享受到Selenium2Library封装好的，特别是对浏览器句柄封装好的keywords。
因此，可以考虑继承Selenium2Library，注意，该库使用了Decorator来将分布在不同类中的public方式暴露并组装成整体。