title: tomcat性能优化及乱码解决
date: 2011-05-11 22:39:50
tags:
	- tomcat
categories:
  - programming
  - java
---

几招让你的Tomcat在生产环境上表现更好

<!--more-->


## 使用thread pool 和 nio来提高性能
在tomcat/conf的server.xml中找到<Connector>节点

``` [xml]
<Connector executor="tomcatThreadPool"  
           port="8080" protocol="org.apache.coyote.http11.Http11NioProtocol"  
           connectionTimeout="20000"   
           redirectPort="8443" URIEncoding="UTF-8"/>  
```
 
要使用nio的话，修改默认的`protocol="HTTP/1.1"` 为 `protocol="org.apache.coyote.http11.Http11NioProtocol"`
 
要使用thread pool的话，添加`executor="tomcatThreadPool"`
 
 
## 将tomcat置于非开发模式来提高性能
在tomcat/conf的web.xml中找到<servlet><servlet-name>jsp</servlet-name>节点

``` [xml]
<init-param>  
<param-name>development</param-name>  
<param-value>false</param-value>  
</init-param>  
   <init-param>  
<param-name>genStringAsCharArray</param-name>  
<param-value>true</param-value>  
</init-param>  
<init-param>  
<param-name>modificationTestInterval</param-name>  
<param-value>1800</param-value>  
</init-param>  
<init-param>  
<param-name>trimSpaces</param-name>  
<param-value>true</param-value>  
</init-param>  
<init-param>  
<param-name>checkInterval</param-name>  
<param-value>1800</param-value>  
</init-param>  
```

## 中文乱码解决（假设使用UTF8编码）
在tomcat/conf的server.xml中找到<Connector>节点，修改URIEncoding="UTF-8"，可以解决GET方式的参数编码
对于POST方式的参数编码，需要在JSP中加入`<%  request.setCharacterEncoding("UTF-8"); %>`
同时在JSP头部加上
`<%@ page language="java" pageEncoding="UTF-8" contentType="text/html; charset=UTF-8"%>`
 
## http 1.1 keep-alive长连接
可以设置tomcat connect中的maxKeepAlive，如果设为1，意味着disable此功能，如果是－1，意味着没有数量限制，默认是100条。
目前浏览器都默认支持此功能，即保留数个持久连接（persist connection），当请求完成后，并不立即释放socket，而是保留一段时间，用于下一次请求。服务器也要以此方式工作。直到某一方显式关闭连接或者timeout。当转向访问其他网站时，而持久连接已经耗尽了，就释放之前某一条持久连接。
 