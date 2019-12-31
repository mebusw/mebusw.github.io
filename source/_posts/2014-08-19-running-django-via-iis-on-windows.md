title: 在Windows上采用IIS来运行Django
date: 2014-08-19 09:44:17
tags:
	- django
	- IIS
	- pyisapie
categories:
	- programming
	- python
	- django
---

## 起

给客户做一个内部软件项目，客户愿意配合敏捷、精益创业的思维，快速交付之后再不断通过用户反馈来调整。
于是就采用基于Python语言v2.7的Django框架V1.4来进行开发。
花了些日子，初具雏形。双方约定先部署，让内部员工开始试用。然后客户把目标服务器交给我了。

Django对Apache或Nginx的支持会方便些（<http://www.jackyshen.com/2012/08/12/running-django-gunicorn-via-nginx/>）。可是，这是一台带有公网IP的托管的Windows Server2003。上面运行有IIS 6.0，并且已经有另外两个web应用在运行。为了尽量能不影响原有应用，就考虑尝试一下在Windows上采用IIS来运行Django。

## 承

于是google一把，结果发现了PyISAPIe...
<!--more-->
[Django官方关于IIS的说明](https://code.djangoproject.com/wiki/DjangoOnWindowsWithIISAndSQLServer)，主要就是要给IIS装一个叫做[pyisapie](http://sourceforge.net/projects/pyisapie/)的插件。

看这个中文的也行。[Windows操作系统上各种Web服务器搭配各种模块部署Django的方法](http://wenku.baidu.com/link?url=XzLll8kPyaQrcJ15t6FCFOWyQnaT9yW1sDYkAt8OKun8cVt9CoB4q0bG3xNOnLkiP0l5SxTLx8lR_N8D9UXwoOz63X0O-Zed854h5spVmN_)

1. 到sourceforge上下到了最新的打包。
2. 给其中的pyisapie.dll的安全属性里面添加权限，是针对`NETWORK SERVICE`添加`read`权限。
3. 在IIS 6.0里面新建一个`网站`，右键属性。在`网站`标签里面，设置端口（比如8000）。在`主目录`标签里面添加`通配符扩展`，选中pyisapie.dll（记得先把这个文件放在django项目目录下）
4. 在IIS管理器中，新建一个`web服务扩展`，还是选中pyisapie.dll，并勾选`允许插件`。
5. 将PyISAPIe下载包中的`Http`目录拷贝到python安装路径下的/lib/site-packages下。
6. 如下修改Http目录中的 Isapi.py文件。然后在IIS里面重启网站就可以了，比如 *http://125.x.x.x:8000/admin*。你自己无需启动django server。

``` py
import sys
sys.path.append(r'd:/xt')  #改为你的django项目目录

os.environ["DJANGO_SETTINGS_MODULE"] = "mysite.settings"
```


## 转

结果不工作！返回的只有500服务器内部出错，以及一串问号。
问号应该是字符编码问题，这是**Windows系统常见坑**之一，像MacOS全都统一用utf8，完全没有这个困扰。看不出出错信息，也不知从何开始调试。从IIS的log文件中也看不到任何线索，只知道IIS收到了请求，却不清楚是否已经转发给django了。

喝了好几杯茶，绞尽脑汁，反复推敲。终于想到会不会是python版本的问题，sourceforge下到的是py26编译的dll，而我用的是py27...看了一下编译说明，需要VS2008，可我用的是Mac，重新编译会很麻烦。

又是一通google，找到了一个py27-x64的dll。放上去试了一下，出错信息不一样了，虽然还是乱码与问号，但是猜到了是x64的DLL不被32bit的Windows支持。终于找到了[py27-32bit编译的DLL](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pyisapie)，这次网页终于显示出来了。这就是**Windows系统常见坑**之二，DLL Hell。

首页显示了，但是一旦需要保存数据，就会出现disk full的调试信息。对了，我用的暂时还是sqlite，还没有转为mysql。估计是文件权限问题，但是修改了一通没用。只好重新syncdb，然后导入数据，终于成功了!

## 合

总结，IIS v5.0-7.0都可以和 django配合，需要注意一个是DLL的编译问题，一个就是IIS的两处插件配置，记得拷贝和修改源文件，最后就是文件权限要注意。

祝大家顺利！
