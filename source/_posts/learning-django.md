title: Django点滴
date: 2014-10-07 20:28:25
tags:
	- python
	- django
categories:
    - programming
    - python
    - django
---

# 目录：

* 会话
* 用户认证
* ORM建模
* ORM存取
* 分页设置
* 缓存
* 生产配置
* 定制Admin
* HTML模板

<!-- more -->

---------

# 会话

会话(Session)用来在服务器端保存单个用户的会话状态，一般用于登录以后。会话ID可以通过cookie来传递给客户端，用于识别当前登录的用户。参考<https://docs.djangoproject.com/en/dev/topics/http/sessions/>

## 开启Session中间件

``` py
MIDDLEWARE_CLASSES = [
...
'django.contrib.sessions.middleware.SessionMiddleware'
...
]
```

## Session引擎

Django默认会使用数据库保存会话状态。此外，还可以设置SESSION_ENGINE 选择使用基于缓存、文件、cookie等方式。
强烈建议将SESSION_COOKIE_HTTPONLY置为True，防止恶意脚本读取到SessionID进而伪造跨站攻击。


## 在View中操作Session对象

Session对象定义在backends.base.SessionBase类中。基本用法如下：

例如，先登录，新建会话

``` py
def login(request):
    m = Member.objects.get(username=request.POST['username'])
    if m.password == request.POST['password']:
        request.session['member_id'] = m.id
        return HttpResponse("You're logged in.")
    else:
        return HttpResponse("Your username and password didn't match.")
```

然后发表评论，检测会话判断是否已登录

``` py
def post_comment(request, new_comment):
    if request.session.get('has_commented', False):
        return HttpResponse("You've already commented.")
    c = comments.Comment(comment=new_comment)
    c.save()
    request.session['has_commented'] = True
    return HttpResponse('Thanks for your comment!')
```

最后登出，清除会话

``` py
def logout(request):
    try:
        del request.session['member_id']
    except KeyError:
        pass
    return HttpResponse("You're logged out.")
```

测试客户端是否支持cookie

``` py
def login(request):
    if request.method == 'POST':
        if request.session.test_cookie_worked():
            request.session.delete_test_cookie()
            return HttpResponse("You're logged in.")
        else:
            return HttpResponse("Please enable cookies and try again.")
    request.session.set_test_cookie()
    return render_to_response('foo/login_form.html')
```



注意：在Django1.4的Test(单元测试)中，要修改并保存session的内容，需要先将其赋值给一个变量，因为每次访问session的属性时都会生成一个新的SessonStore。
``` py
def test_something(self):
    session = self.client.session
    session['somekey'] = 'test'
    session.save()
```

-----

# 用户认证

参见<https://docs.djangoproject.com/en/dev/topics/auth/default/>

## 启用中间件和模块

在settings.py中， MIDDLEWARE_CLASSES启用SessionMiddleware 和AuthenticationMiddleware ；同时， INSTALLED_APPS启用'django.contrib.auth' 和'django.contrib.contenttypes' 。


## 用户对象

启用相应中间件和模块并syncdb以后，Django就在数据库中为User对象创建了表。甚至可以用/admin页面(只要你启用了的话)来管理User。

``` py
from django.contrib.auth.models import User
user = User.objects.create_user('john', 'lennon@thebeatles.com', 'johnpassword')
# 此时，新的User实例已经保存到数据库了

user.last_name = 'Lennon'
user.save()

u = User.objects.get(username__exact='john')
u.set_password('new password')
u.save()
```

Django自动会为密码加密，可以用下列方式来验证某个用户。

``` py
from django.contrib.auth import authenticate
user = authenticate(username='john', password='secret')
if user is not None:
    # the password verified for the user
    if user.is_active:
        print("User is valid, active and authenticated")
    else:
        print("The password is valid, but the account has been disabled!")
else:
    # the authentication system was unable to verify the username and password
    print("The username and password were incorrect.")
```


## 权限

用户可以加入某个权限组(Group)，或者单独指定多个权限。默认

``` py
myuser.groups = [group_list]
myuser.groups.add(group, group, ...)
myuser.groups.remove(group, group, ...)
myuser.groups.clear()
myuser.user_permissions = [permission_list]
myuser.user_permissions.add(permission, permission, ...)
myuser.user_permissions.remove(permission, permission, ...)
myuser.user_permissions.clear()
```

新增某类权限
``` py
from django.contrib.auth.models import Group, Permission
from django.contrib.contenttypes.models import ContentType

content_type = ContentType.objects.get(app_label='myapp', model='BlogPost')
permission = Permission.objects.create(codename='can_publish',
                                       name='Can Publish Posts',
                                       content_type=content_type)
```
判断用户是否拥有某个权限
在Web请求里面进行验证
登入某个用户，并自动创建Session。

``` py
from django.contrib.auth import authenticate, login

def my_view(request):
    username = request.POST['username']
    password = request.POST['password']
    user = authenticate(username=username, password=password)
    if user is not None:
        if user.is_active:
            login(request, user)
            # Redirect to a success page.
        else:
            # Return a 'disabled account' error message
    else:
        # Return an 'invalid login' error message.
```

登出
``` py
from django.contrib.auth import logout

def logout_view(request):
    logout(request)
    # Redirect to a success page.
限制未登录用户。

from django.shortcuts import redirect

def my_view(request):
    if not request.user.is_authenticated():
        return redirect('/login/?next=%s' % request.path)
    # ...
```


强制要求验证，使用注解。可以指定要跳转的登陆URL，并在url.py中适当配置。
``` py
from django.contrib.auth.decorators import login_required

@login_required(login_url='/accounts/login/')
def my_view(request):
    user = request.user
```

还可以对登录要求一些规则验证或者权限验证，不符合就跳回登录URL。

``` py
from django.contrib.auth.decorators import user_passes_test

def email_check(user):
    return '@example.com' in user.email

@user_passes_test(email_check, login_url='/login/')
def my_view(request):
    ...

from django.contrib.auth.decorators import permission_required

@permission_required('polls.can_vote', login_url='/loginpage/')
def my_view(request):
    ...
```


在模板中使用

``` django
{% if user.is_authenticated %}
    <p>Welcome, {{ user.username }}. Thanks for logging in.</p>
{% else %}
    <p>Welcome, new user. Please log in.</p>
{% endif %}

{% if perms.foo %}
    <p>You have permission to do something in the foo app.</p>
    {% if perms.foo.can_vote %}
        <p>You can vote!</p>
    {% endif %}
    {% if perms.foo.can_drive %}
        <p>You can drive!</p>
    {% endif %}
{% else %}
    <p>You don't have permission to do anything in the foo app.</p>
{% endif %}
```

------

## ORM建模

ORM为数据库建模带来了便利。
参见<https://docs.djangoproject.com/en/dev/topics/db/models/>, <https://docs.djangoproject.com/en/dev/ref/models/fields/#model-field-types>


例如下列定义会自动生成SQL语句：
``` sql
CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```
``` py
from django.db import models

class Person(models.Model):
    first_name = models.CharField(max_length=30)
    last_name = models.CharField(max_length=30)

CREATE TABLE myapp_person (
    "id" serial NOT NULL PRIMARY KEY,
    "first_name" varchar(30) NOT NULL,
    "last_name" varchar(30) NOT NULL
);
```

## 常见字段域

``` py
AutoField(**options)
BigIntegerField([**options])
BooleanField(**options)
CharField(max_length=None[, **options])
CommaSeparatedIntegerField(max_length=None[, **options])
DateField([auto_now=False, auto_now_add=False, **options])
DateTimeField([auto_now=False, auto_now_add=False, **options])
DecimalField(max_digits=None, decimal_places=None[, **options])
EmailField([max_length=75, **options])
FileField(upload_to=None[, max_length=100, **options])
FloatField([**options])
ImageField(upload_to=None[, height_field=None, width_field=None, max_length=100, **options])
IntegerField([**options])
IPAddressField([**options])
TextField([**options])
TimeField([auto_now=False, auto_now_add=False, **options])
URLField([max_length=200, **options])
```

## 字段域的参数(options)

null 是否可以为null
blank 是否可以为空
default 缺省值
choices 枚举类型

``` py
class Student(models.Model):
    FRESHMAN = 'FR'
    SOPHOMORE = 'SO'
    JUNIOR = 'JR'
    SENIOR = 'SR'
    YEAR_IN_SCHOOL_CHOICES = (
        (FRESHMAN, 'Freshman'),
        (SOPHOMORE, 'Sophomore'),
        (JUNIOR, 'Junior'),
        (SENIOR, 'Senior'),
    )
    year_in_school = models.CharField(max_length=2,
                                      choices=YEAR_IN_SCHOOL_CHOICES,
                                      default=FRESHMAN)

    def is_upperclass(self):
        return self.year_in_school in (self.JUNIOR, self.SENIOR)
```

primary_key 是否为主键。如果不指定主键，会自动生成id字段作为主键。
unique 是否唯一
unique_for_date 是否对日期唯一
validator 指定验证器
verbose_name 更友好的显示名称（注意，django/admin 用到的是覆盖后的model.Model.__unicode__()方法）

## 关系字段域

* 外键：

``` py
class Car(models.Model):
    manufacturer = models.ForeignKey('Manufacturer')
    # ...

class Manufacturer(models.Model):
    # ...
```

* 多对多：

``` py
class Topping(models.Model):
    # ...

class Pizza(models.Model):
    # ...
    toppings = models.ManyToManyField(Topping)
```

* 多对多间接关系（注意 Person和Group不是直接关联的，而是通过 through='Membership'）：

``` py
class Person(models.Model):
    name = models.CharField(max_length=128)

    def __unicode__(self):
        return self.name

class Group(models.Model):
    name = models.CharField(max_length=128)
    members = models.ManyToManyField(Person, through='Membership')

    def __unicode__(self):
        return self.name

class Membership(models.Model):
    person = models.ForeignKey(Person)
    group = models.ForeignKey(Group)
    date_joined = models.DateField()
    invite_reason = models.CharField(max_length=64)

>>> ringo = Person.objects.create(name="Ringo Starr")
>>> paul = Person.objects.create(name="Paul McCartney")
>>> beatles = Group.objects.create(name="The Beatles")
>>> m1 = Membership(person=ringo, group=beatles,
...     date_joined=date(1962, 8, 16),
...     invite_reason= "Needed a new drummer.")
>>> m1.save()
>>> beatles.members.all()
[<Person: Ringo Starr>]
>>> ringo.group_set.all()
[<Group: The Beatles>]
>>> m2 = Membership.objects.create(person=paul, group=beatles,
...     date_joined=date(1960, 8, 1),
...     invite_reason= "Wanted to form a band.")
>>> beatles.members.all()
[<Person: Ringo Starr>, <Person: Paul McCartney>]

# THIS WILL NOT WORK
>>> beatles.members.add(john)
# NEITHER WILL THIS
>>> beatles.members.create(name="George Harrison")
# AND NEITHER WILL THIS
>>> beatles.members = [john, paul, ringo, george]
```

-------

# ORM对象存取

ps: 看了昆汀塔伦蒂诺的新片，Django原来d不发音啊，所以应该念“姜戈”(类似jungle的发音)。

无论是RoR还是Django，甚至颇具野心的前端框架EmberJs.data，将ORM集成于框架之中并赋予方便存储的API，大大提高了开发效率。不像Java世界中的Hibernate弄了个HQL查询语言，Django这里利用动态语言的优势，令CRUD语义更加自然(EJB3.0中JPA其实也有类似的意思)。当然你还是可以用纯粹的sql直接操作。参见<https://docs.djangoproject.com/en/dev/ref/models/instances/>


这里先跳过如何用django.Models对数据库建模。假设已经存在下列ORM对象定义，`Blog<-(一对多)->>Entry<<-(多对多)->Author`。

``` py
class Blog(models.Model):
    name = models.CharField(max_length=100)
    tagline = models.TextField()

    def __unicode__(self):
        return self.name

class Author(models.Model):
    name = models.CharField(max_length=50)
    email = models.EmailField()

    def __unicode__(self):
        return self.name

class Entry(models.Model):
    blog = models.ForeignKey(Blog)
    headline = models.CharField(max_length=255)
    body_text = models.TextField()
    pub_date = models.DateField()
    mod_date = models.DateField()
    authors = models.ManyToManyField(Author)
    n_comments = models.IntegerField()
    n_pingbacks = models.IntegerField()
    rating = models.IntegerField()

    def __unicode__(self):
        return self.headline
```

## 基本CRUD操作

``` py
from blog.models import Blog, Entry, Author
blog = Blog(name='Beatles Blog', tagline='All the latest Beatles news.')  
blog.save()    

entry = Entry.objects.get(pk=1)  
blog = Blog.objects.get(name="Beatles Blog")  
entry.blog = blog   # ForeignKey  
entry.save()    

john = Author.objects.create(name="John")  
paul = Author.objects.create(name="Paul")  
george = Author.objects.create(name="George")  
ringo = Author.objects.create(name="Ringo")  
entry.authors.add(john, paul, george, ringo)   # ManyToManyField    

all_author = Author.objects.all()    

blog.delete()    

Entry.objects.filter(pub_date__year=2007).update(headline='Everything is the same')  
Entry.objects.all().update(n_pingbacks=F('n_pingbacks') + 1)  


```


## 查询过滤器

分为filter(**kwargs) 和exclude(**kwargs)两种，前者是包含条件查询，后者是不包含条件查询。都返回QuerySet集合，并且可以级联查询。
如果明确地查询唯一对象，可以使用get(**kwargs)
如果查询所有对象，使用all()就行。

``` py
Entry.objects.filter(  
     headline__startswith='What'  
 ).exclude(  
     pub_date__gte=datetime.date.today()  
 ).filter(  
     pub_date__gte=datetime(2005, 1, 30)  
 )
```


查询条件的特殊情况：

``` py
### blog的某一个entry必须同时满足两个条件。
Blog.objects.filter(entry__headline__contains='Lennon', entry__pub_date__year=2008)  


### blog的某个entry满足前一条件，同时其另一个entry满足后一个条件。返回包含这样2哥entry的blog，串行查询。 
Blog.objects.filter(entry__headline__contains='Lennon').filter(entry__pub_date__year=2008)  
```

### 查询表达式

（注意字段名后面是两个下划线，再加查询表达式）。
https://docs.djangoproject.com/en/dev/topics/db/queries/#field-lookups

``` py
### 比较,lte, le, gt, gte ：
### SELECT * FROM blog_entry WHERE pub_date <= '2006-01-01';  
Entry.objects.filter(pub_date__lte='2006-01-01')   


### (精确)相等, id, pk：
### SELECT ... WHERE id = 14;  
Blog.objects.get(id=14) 

### 包含：
### SELECT ... WHERE headline LIKE '%Lennon%';  
Entry.objects.get(headline__contains='Lennon')  
```
此外，还有icontains、startswith、endswith、isnull, in, 等


关系传递的查询表达式

``` py
Entry.objects.filter(blog__name__exact='Beatles Blog')  
Blog.objects.filter(entry__authors__name='Lennon')  
Blog.objects.filter(entry__authors__isnull=False, entry__authors__name__isnull=True) 
```

对某个对象的外键或关联对象直接作为查询条件。
``` py
Entry.objects.filter(blog__name__exact='Beatles Blog')  
Blog.objects.filter(entry__authors__name='Lennon')  
Blog.objects.filter(entry__authors__isnull=False, entry__authors__name__isnull=True) 
```


分片查询

以下等同于SELECT * FROM Entry OFFSET 5 LIMIT 3

``` py
Entry.objects.all()[5:8]
```

结果排序

以下对结果按某字段排序，再分片
``` py
Entry.objects.order_by('headline')[0:1]
```


## F表达式(F Expression)

之前的查询表达式的右侧都是常量，而F表达式用于引用被查询对象的其他字段。
(n_xxx表示xxx字段按照数字类型处理。)

``` py
from django.db.models import F  
from datetime import timedelta  
  
Entry.objects.filter(n_comments__gt=F('n_pingbacks') * 2)  
Entry.objects.filter(rating__lt=F('n_comments') + F('n_pingbacks'))  
Entry.objects.filter(authors__name=F('blog__name'))  
Entry.objects.filter(mod_date__gt=F('pub_date') + timedelta(days=3))  
F('somefield').bitand(16) 
```

## 利用Q对象(Q Object)的复杂查询

之前的查询组合都是”逻辑与”的简单查询。对于”逻辑或”等复杂查询，一旦查询条件使用了Q对象，所有条件就都必须是Q对象。Q对象可以用& (And), | (Or), ~ (Not)来运算。

``` py
from django.db.models import Q  
  
### SELECT * from polls WHERE question LIKE 'Who%' AND (pub_date = '2005-05-02' OR pub_date = '2005-05-06')  
Poll.objects.get(  
    Q(question__startswith='Who'),  
    Q(pub_date=date(2005, 5, 2)) | Q(pub_date=date(2005, 5, 6))  
) 
```

## 复制对象

将pk设为None后保存，即可生成新记录。如果对象有继承关系，还要将pk和id都设为None才行。

``` py
blog = Blog(name='My blog', tagline='Blogging is easy')  
blog.save() # blog.pk == 1  
  
blog.pk = None  
blog.save() # blog.pk == 2  
  
class ThemeBlog(Blog):  
    theme = models.CharField(max_length=200)  
  
django_blog = ThemeBlog(name='Django', tagline='Django is easy', theme='python')  
django_blog.save() # django_blog.pk == 3  
  
django_blog.pk = None  
django_blog.id = None  
django_blog.save() # django_blog.pk == 4  
```

注意，复制对象不会复制原有对象的关系，需要手动复制。

``` py
entry = Entry.objects.all()[0] # some previous entry
old_authors = entry.authors.all()
entry.pk = None
entry.save()
entry.authors = old_authors # saves new many2many relations
```

## 访问关系对象

无论是外键，还是一对多，多对多关系，都需要访问其他关系对象。

一对一关系时，正向访问直接用字段名，反向用字段名加上”entry”后缀。

一对多关系时，正向访问用字段名，反向访问用字段名加上"_set”后缀(如果定义了别名，则采用别名)。关系对象集可以用add, remove, create, clear,count, filter等来操作。

多对多关系时，正向访问用字段名加上".all()”后缀。反向同一对多。

``` py
Entry.objects.get(id=2).blog  
Entry.objects.get(id=2).authors.filter(name__contains='John')  
Blog.objects.get(id=1).entry_set.all()  
```

User对象与Profile对象，建议使用一对一关系，用user.profile就可访问。

``` py
class Profile(models.Model):
   """model to represent additional information about users"""
   user = models.OneToOneField(User)
```

-----


## 分页设置
Django提供一个 Pagination 类用于分页.

实际运用：

先来到views里来改写一个之前的简单视图。 只要过一遍上面的基本用法，这里都不难理解。有一点：关于page = int(request.GET.get('page','1')) 可以参看django文档的 QueryDict objects 部分。get('page','1')， 如果没有对应的page键，就返回默认1。 如果page键值不是数字，也在异常中设为1，这个可以改改url中的page=xxx来试试。

``` py
from django.core.paginator import Paginator, InvalidPage, EmptyPage

def index(request):
    posts_list = blogPost.objects.all()
    
    #####ADD
    page_size=2
    paginator = Paginator(posts_list, page_size)
    try:
        page = int(request.GET.get('page','1'))
    except ValueError:
        page = 1
        print page
    
    try:
        posts = paginator.page(page)
    except (EmptyPage, InvalidPage):
        posts = paginator.page(paginator.num_pages)         
    #####ADD
    
    t = loader.get_template("index.html")
    c = Context({ 'posts' : posts })
    return HttpResponse(t.render(c))
```

模板中添加显示页数以及前进后退
``` py
                    <div class="pagination">
                        <span class="step-links">
                            {% if posts.has_previous %}
                                <a href="?page={{ posts.previous_page_number }}">prev</a>
                            {% else %}
                                <span class='empty'></span>
                            {% endif %}
                            <span class="current">
                                Page {{ posts.number }} of {{ posts.paginator.num_pages }}
                            </span>
                            {% if posts.has_next %}
                                <a href="?page={{ posts.next_page_number }}">next</a>
                            {% else %}
                                <span class='empty'></span>                             
                            {% endif %}
                        </span>
                    </div>
```
------

# 缓存
Cache缓存是web框架以及REST/SOA架构的设计原则之一。

## Caching基本过程：
```
IF 存在某个页面或数据的缓存
THEN 返回缓存中的页面或数据
ELSE
重新生成页面或获取数据
保存新页面或数据
返回新页面或数据 
```



## Django如何连接Cache

根据不同缓存方案，在settings.py进行配置。

``` py
# 以Memcached为例
CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.memcached.MemcachedCache',
        'LOCATION': '127.0.0.1:11211',
        'TIMEOUT': 60,
        'OPTIONS': {
            'MAX_ENTRIES': 1000
        }
    }
}

#本地内存为例

CACHES = {
    'default': {
        'BACKEND': 'django.core.cache.backends.locmem.LocMemCache',
        'LOCATION': 'unique-snowflake'
    }
}
```

## 不同的缓存策略

### 全站缓存

在Settings.py中加入cache middleware，注意顺序。
因为FetchFromCacheMiddleware在request阶段执行，该阶段的middleware执行顺序是：正向，出现在最上的最先执行（因此FetchFromCacheMiddleware在其他处理头信息的middleware之后运行）；而UpdateCacheMiddleware在response阶段执行，该阶段的middleware执行顺序是：反向，出现在最上却最后执行（因此UpdateCacheMiddleware在其他任何更改头信息的middleware之后运行）。


``` py
MIDDLEWARE_CLASSES = (
   'django.middleware.cache.UpdateCacheMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',    #adds Cookie
    ### GZipMiddleware adds Accept-Encoding
    ### LocaleMiddleware adds Accept-Language
    'django.middleware.common.CommonMiddleware',
   'django.middleware.cache.FetchFromCacheMiddleware',
)

CACHE_MIDDLEWARE_SECONDS = 3  ### in seconds
```

### 单个View缓存

利用Decorator针对单个视图进行缓存

``` py
from django.views.decorators.cache import cache_page

@cache_page(60 * 15) ### i.e. 15 minutes
def my_view(request):
    ...

```

### 模板内容缓存

```
{ % load cache % }
{ % cache 500 sidebar % }
    .. sidebar ..
{ % endcache % }
```

### 底层缓存API，手工操作

``` py
>>> from django.core.cache import cache 
>>> cache.set('my_key', 'hello, world!', 30) 
>>> cache.get('my_key') 'hello, world!' 
# Wait 30 seconds for 'my_key' to expire... 
>>> cache.get('my_key') 
None 
>>> cache.set_many({'a': 1, 'b': 2, 'c': 3}, version=1) 
>>> cache.get_many(['a', 'b', 'c']) {'a': 1, 'b': 2, 'c': 3} 
>>> cache.delete('a')

``` 


------

# 生产配置

---------
# 定制Django-Admin

---------
# HTML模板