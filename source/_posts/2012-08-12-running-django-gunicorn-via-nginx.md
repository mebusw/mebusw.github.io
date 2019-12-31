title: 在Linux上采用Nginx来运行Django+Gunicorn
date: 2012-08-12 11:31:28
tags:
	- django
	- nginx
	- gunicorn
categories:
	- programming
	- python
	- django
---


项目是运行在Ubuntu上，基于Django 1.4开发。我想用`gunicorn`作为生产服务器，并用`nginx`作为web服务器，以此提高性能。


### 考虑django已经可以正常工作，现在安装gunicorn和nginx

``` bash
$ sudo apt-get install gunicorn nginx
```

### 将gunicorn添加到settings.py
<!--more-->

``` py
INSTALLED_APPS = (  
    'django.contrib.auth',  
    'django.contrib.contenttypes',  
    'django.contrib.sessions',  
    'django.contrib.sites',  
    'django.contrib.messages',  
    'django.contrib.staticfiles',  
    # Uncomment the next line to enable the admin:  
    'django.contrib.admin',  
    # Uncomment the next line to enable admin documentation:  
    # 'django.contrib.admindocs',  
    'myApplication'  
    'djangorestframework',  
    'gunicorn',  
)  
```

### 用gunicorn做为生产服务器来运行django
``` bash
$ python manage.py run_gunicorn
```


### 在nginx配置(/etc/nginx/nginx.conf)中添加新的web服务器

``` bash
$ sudo vi /etc/nginx/nginx.conf  
``` 

编辑配置

``` conf 
http {  
   server {  
       listen          80;  
       server_name     192.168.85.172;  #yousite.com  
       #access_log      /var/log/your_website_log main;    
       location / {  
             proxy_pass http://localhost:8000/;  
             proxy_redirect  off;  
        }  
    }  
}  
```

注意: 这里的192.168.85.172是测试服务器的本地IP。 `server_name`本来默认为 *'localhost'*, 但是如果不改成这样的我无法成功绑定80端口。否则的话，在我的Ubuntu 12.04上，只能绑定其他端口（比如8001）。

### 测试成功

现在访问nginx <http://localhost/admin> 实际上就重定向到了 <http://localhost:8000/admin>，也就是运行在gunicorn的django应用。



### 如果你用Jetty/Tomcat来运行J2EE应用的话，也可以采用nginx...
在启动jetty/tomcat后，把配置中的`proxy_pass`改为比如 *'proxy_pass http://localhost:8080/;'*就行。
