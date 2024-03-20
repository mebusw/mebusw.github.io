title: Jenkins CI连接Mac电脑失败
date: 2011-07-20 21:40:45
tags:
	- jenkins
	- mac

categories:
    - agile
    - engineering
---


Jenkins是著名的开源CI工具，也很易用。其前身是Hudson，自从Hudson被Oracle收购就被迫改名了。
(关于iPhone开发的CI,参见: [命令行运行iphone模拟器以及运行Unit test](http://www.jackyshen.com/2011-07-19-run-unittest-in-iphone-simulator-from-terminal))

我的Jenkins安装在Linux上，但是iphone app却只能在Mac机上进行编译和运行单元测试，所以需要给Jenkins建立带tag 的Node，并且把iphone工程也打上相同的tag，这样就可以强制该工程在Mac环境上编译，而不是在本机Linux上。

<!--more-->

## 解决之道


Mac机的sshd默认是打开的，从Linux机命令行连接过去没有问题，但是Jenkins总是提示验证失
败，why？


```
[07/20/11 17:00:28] [SSH] Authentication failed.
[07/20/11 17:00:28] [SSH] Connection closed.
hudson.AbortException: Authentication failed.
	at hudson.plugins.sshslaves.SSHLauncher.openConnection(SSHLauncher.java:600)
	at hudson.plugins.sshslaves.SSHLauncher.launch(SSHLauncher.java:198)
	at hudson.slaves.SlaveComputer$1.call(SlaveComputer.java:199)
	at java.util.concurrent.FutureTask$Sync.innerRun(FutureTask.java:334)
	at java.util.concurrent.FutureTask.run(FutureTask.java:166)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1110)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:603)
	at java.lang.Thread.run(Thread.java:679)
```

经过研究，需要按照以下来修改Mac机上的 `/etc/sshd_config` 文件（该项默认为no）：

	PasswordAuthentication yes


令人不解，按理说这个选项为no的话，之前在命令行中ssh应该也连不上才是。

## 关于在Jenkins上授权GIT
该GIT插件只能输入远程git repo的ssh连接地址,但是没有地方输入用户密码.因此`git clone`时会提示密码失败,导致build失败。
解决办法：在Mac电脑上生成RSA公钥，授权MAC机上访问CI服务器不再需要输入密码即可。
```
ssh-keygen -t rsa
less ~/.ssh/id_rsa.pub
```
用文本编辑器,将公钥的信息复制到CI服务器上,并保存。从此，从MAC机ssh到CI服务器不会再提示输入密码了。
```
sudo emacs ~/.ssh/authorized_keys```
