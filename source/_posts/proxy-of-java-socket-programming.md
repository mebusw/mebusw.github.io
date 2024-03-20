title: Java Socket编程中加入代理
date: 2011-04-29 23:44:31
tags:
	- socket
	- proxy
categories:
	- programming
	- java	
---

有些时候我们的网络不能直接连接到外网, 需要使用http或是https或是socket代理来连接到外网, 这里是java使用代理连接到外网的一些方法,:方法一使用系统属性来完成代理设置, 这种方法比较简单, 但是不能对单独的连接来设置代理:
<!--more-->

```[java]
public static void main(String[] args) {  
    Properties prop = System.getProperties();  
    // 设置http访问要使用的代理服务器的地址  
    prop.setProperty("http.proxyHost", "192.168.0.254");  
    // 设置http访问要使用的代理服务器的端口  
    prop.setProperty("http.proxyPort", "8080");  
    // 设置不需要通过代理服务器访问的主机，可以使用*通配符，多个地址用|分隔  
    prop.setProperty("http.nonProxyHosts", "localhost|192.168.0.*");  
    // 设置安全访问使用的代理服务器地址与端口  
    // 它没有https.nonProxyHosts属性，它按照http.nonProxyHosts 中设置的规则访问  
    prop.setProperty("https.proxyHost", "192.168.0.254");  
    prop.setProperty("https.proxyPort", "443");  
    // 使用ftp代理服务器的主机、端口以及不需要使用ftp代理服务器的主机  
    prop.setProperty("ftp.proxyHost", "192.168.0.254");  
    prop.setProperty("ftp.proxyPort", "2121");  
    prop.setProperty("ftp.nonProxyHosts", "localhost|192.168.0.*");  
    // socks代理服务器的地址与端口  
    prop.setProperty("socksProxyHost", "192.168.0.254");  
    prop.setProperty("socksProxyPort", "8000");  
    // 设置登陆到代理服务器的用户名和密码  
    Authenticator.setDefault(new MyAuthenticator("userName", "Password"));  
}  
static class MyAuthenticator extends Authenticator {  
    private String user = "";  
    private String password = "";  
    public MyAuthenticator(String user, String password) {  
        this.user = user;  
        this.password = password;  
    }  
    protected PasswordAuthentication getPasswordAuthentication() {  
        return new PasswordAuthentication(user, password.toCharArray());  
    }  
}  
```

方法二使用Proxy来对每个连接实现代理, 这种方法只能在jdk 1.5以上的版本使用(包含jdk1.5), 优点是可以单独的设置每个连接的代理, 缺点是设置比较麻烦:
```[java]
public static void main(String[] args) {  
    try {  
        URL url = new URL("http://www.baidu.com");  
        // 创建代理服务器  
        InetSocketAddress addr = new InetSocketAddress("192.168.0.254",  
                8080);  
        // Proxy proxy = new Proxy(Proxy.Type.SOCKS, addr); // Socket 代理  
        Proxy proxy = new Proxy(Proxy.Type.HTTP, addr); // http 代理  
        // 如果我们知道代理server的名字, 可以直接使用  
        // 结束  
        URLConnection conn = url.openConnection(proxy);  
        InputStream in = conn.getInputStream();  
        // InputStream in = url.openStream();  
        String s = IOUtils.toString(in);  
        System.out.println(s);  
    } catch (Exception e) {  
        e.printStackTrace();  
    }  
}  
```