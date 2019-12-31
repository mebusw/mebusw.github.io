title: 强制Gradle/Maven刷新缓存并重新从Nexus下载依赖jar包
date: 2015-10-18 09:49:31
tags:
    - gradle
    - maven
    - nexus
    - java

categories:
    - agile
    - tools
---

最近需要搭建一个Nexus私服，完全不能连接外网的那种，各种Jar包都是手动拷过来的，碰到需要gradle和maven强制重新下载依赖的问题。

# 问题
第一次上传某个jar包（比如junit-4.12.jar）到Nexus上，然后调用`gradle build`可以正确下载到依赖包。但如果手动删掉了本地缓存的jar包(在`~/.gradle`下)，这时从Nexus的下载过程中断，或者Nexus上暂时不存在这个jar包，那么即使Nexus恢复了正常下载，下次执行`gradle build`时就一直提示不能够找到jar包。

    FAILURE: Build failed with an exception.

    - What went wrong:
    Could not resolve all dependencies for configuration ':testCompile'.
    > Could not find junit:junit:4.12.
      Searched in the following locations:
          http://localhost:8081/nexus/content/groups/public/junit/junit/4.12/junit-4.12.pom
          http://localhost:8081/nexus/content/groups/public/junit/junit/4.12/junit-4.12.jar
      Required by:
          org.codehaus.sonar:example-java-maven:1.0-SNAPSHOT


# 解决方案
回到自己的工作目录下，带参数执行gradle强制刷新`~/.gradle`下的文件缓存

```
gradle build --refresh-dependencies
```

<!-- more -->

参考 [Gradle manual](https://docs.gradle.org/current/userguide/dependency_management.html)

> The --refresh-dependencies option tells Gradle to ignore all cached entries for resolved modules and artifacts. A fresh resolve will be performed against all configured repositories, with dynamic versions recalculated, modules refreshed, and artifacts downloaded.


-------

如果是Maven遇到不能刷新本地依赖的话，需要做两件事情：
1. 到`~/.m2`下，看看jar包路径是不是带有".lastUpdated"，有的话就删除之。
2. 带参数强制刷新`mvn build -U`

--------

另外，如果从Nexus将Jar包删除，或重新上传时(需要设置Configuration->Deployment Policy为Allow Redeploy)，有时需要在Browse Storage中，右键Jar包路径，先"Update Index"再"Expire Cache"。注意这里是刷新Nexus自身的缓存和索引，不是开发目录本地的缓存！


