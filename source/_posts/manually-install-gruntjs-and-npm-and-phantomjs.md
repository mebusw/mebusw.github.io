title: 手工安装 grunt.js/npm/phantomJs 一些经验
tags:
  - gruntjs
  - jasmine
  - nodejs
  - npm
categories:
    - programming
    - javascript
---

grunt.js是node.js的一个组件，本身还带有很多plug-in，用于javascript应用程序的构建管理及依赖管理，诸如lint, copy, concat, minify,  unit test等，有点象Maven。

有连接外网的机器，可以很方便的利用其本身的依赖管理NPM安装。但是企业内网就有一些限制，下面就如何在无外网连接的linux主机安装grunt.js等进行说明。

&nbsp;

主机是centOS,之前已经安装了RPM包的gcc。

下载node.js源码，拷贝到目标机，然后make，这个还好。

下载grunt.js，同样拷贝到目标机。注意grunt.js应该使用(npm -g install grunt)作为全局安装。在linux系统下，结果是在/usr/lib/node_modules下。在winXP下，是在 C:\Documents and Settings\&lt;username&gt;\Application Data\npm\node_modules 下。其他grunt的plugin，如果不是全局module，一般都是安装在项目下，即&lt;project&gt;/node_modules。

&nbsp;

我们希望grunt的这些module能被目标机的多个项目共享，毕竟没有网络，也不想拷来拷去。在https://npmjs.org/doc/link.html 提供了一种npm link的方法，不太好使。其实不用它这么麻烦。将所需的grunt module都拷到目标机的某个目录下的node_modules，然后直接在每个项目下新建个指向该目录的符号链接：

cd &lt;project&gt;

ln -s  &lt;somewhere&gt;/node_modules/      node_modules

&nbsp;

ok，在项目下运行grunt吧！

（要是使用了jasmine进行单元测试，还需要安装grunt-jasmine-runner模块及PhantomJS）
1.      Grunt.js/node.js should be quite easy toonce-click install if this centOS5 server has a internet connection, but, forthis server, I have to copy the .tar manually, then copy all modules andplugins by finding the right place to put

2.      Grunt-jasmine-runner require phantomJs to rununit test. At first, I tried to run the complied version for linux, but itrequires libgc v2.9+ , which finally I realize centOS5 can’t afford such highversion (even forcely compile that lib with low version gcc compiler, possiblywill make the low version linux core crash).

3.      Then I tried to compile phantomJs 1.7 manually,which depends on 30+ libs. Those can be download from[http://mirror.centos.org/centos/5/os/x86_64/CentOS/](http://mirror.centos.org/centos/5/os/x86_64/CentOS/)then copy to server, and run with rpm command in turn. Fortunately, it works

4.      The last tricky one, the passed unit test failson that server by run ‘grunt jasmine’.

&lt;pre&gt;Running "jasmine" task

Testing jasmine specs via phantom
...
[D] ["phantomjs","onResourceReceived","GET http://127.0.0.1:8888/test/spec/CommonTest.js"]
[D] ["phantomjs","onError","ReferenceError: Can't find variable: describe",[{"file":"http://127.0.0.1:8888/test/spec/CommonTest.js","line":31,"function":""}]]
ReferenceError: Can't find variable: describe
...&lt;/pre&gt;

At last, it’s caused by that: I createda symbolic link for “node_modules” to re-use common module, but it preventgrunt-jasmine-runner to fetch sth. (not sure yet). To resolve it, need to copythe “node_modules” files directly under project folder.