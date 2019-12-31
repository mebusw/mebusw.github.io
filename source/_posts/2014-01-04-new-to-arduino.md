title: Arduino初探
categories:
  - programming
  - arduino

tags:
  - arduino
  - maker
---

那天看了极客范DIY系列：便携式树莓派报警装置深受启发，于是从深圳带了个Arduino回来，重温当年的模电数电！不过确实比当年的单片机更容易玩，可以集中精力发挥创造力！另外这有个基于RaspBerryPi的视频，老子教儿子做报警器。http://t.cn/8kkOmnn

是从taobao买的A货，型号标注为Arduino Uno. 从arduino.cc/en/Main/Software 下载官方IDE。用USB线连上MacBook，发现ArduinoIDE无法上传程序到板卡，搜了一下官网，得知Uno本来是免驱动的，但我买的这卡上面带有明显的FTDI芯片一枚，所以又去http://www.ftdichip.com/Drivers/VCP.htm 下载MacOS驱动，然后终于连上了USB串口，做了呼吸灯和光敏灯。

官方函数手册：http://arduino.cc/en/Tutorial/HomePage

![](http://ww4.sinaimg.cn/mw1024/3bb73a47tw1ec7k3wkl9mj20hs0nsgnw.jpg)