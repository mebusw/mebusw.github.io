title: 利用python yielding创建协程将异步编程同步化
date: 2015-05-21 00:53:21
tags:
    - yield
    - async
    - gevent
    - greenlet
    - python

categories:
    - programming
    - python
---
# 目录

* 回顾同步与异步编程
* 回顾多线程编程
* yield与协程
* 异步编程同步化


<!-- more -->

# 回顾同步与异步编程
同步编程即线性化编程，代码按照既定顺序执行，上一条语句执行完才会执行下一条，否则就一直等在那里。
但是许多实际操作都是CPU 密集型任务和 IO 密集型任务，比如网络请求，此时不能让这些任务阻塞主线程的工作，于是就会采用异步编程。

![](http://upload.chinaz.com/2013/0102/1357115956379.png)

异步的标准元素就是回调函数（Callback, 后来衍生出Promise/Deferred概念），主线程发起一个异步任务，让其自己到一边去工作，当其完成后，会通过执行预先指定的回调函数完成后续任务，然后返回主线程。在异步任务执行过程中，主线程无需等待和阻塞，可以继续处理其他任务。

下例大家并不陌生，是jQuery标准发送http异步请求的方式。

``` js
$.ajax({
    url:"/echo/json/",
    success: function(response)
    {
       console.info(response.name);
    }
});
```

而并发的核心思想在于，大的任务可以分解成一系列的子任务，后者可以被调度成 同时执行或异步执行，而不是一次一个地或者同步地执行。两个子任务之间的 切换也就是上下文切换。


# 回顾多线程编程

当主线程发起异步任务，这个任务跑到哪里去工作了呢？这就说到多线程（包括多进程）编程，一个主线程可以主动创建多个子线程，然后将任务交给子线程，每个子线程拥有自己的堆栈空间。操作系统可以通过分时的方式让同一个CPU轮流调度各个线程，编程人员无需关心操作系统是如何工作的。

但是如果需要在多个线程之间通信，则需要编程人员自己写代码来控制线程之间的协作（利用锁或信号量）以及通信（利用管道、队列等）。


## 经典的Producer-Consumer问题
![](https://www.rabbitmq.com/img/tutorials/python-one.png)

这个问题说的是有两方进行通信和协作，一方只负责生产内容，另一方只负责消费内容。消费者并不知道，也无需知道生产者何时生产，只是当有内容生产出来负责消费即可，没有内容时就等待。这是一个经典的异步问题。

## Threading/Queue方案

传统的解决方案即是采用多线程来实现，生产者和消费者分别处于不同的线程或进程中，由操作系统进行调度。来看一篇经典的多线程教程中的例子，是不是很像Java风格？---啰嗦。

``` py
import threading
import time
import logging
import random
import Queue

logging.basicConfig(level=logging.DEBUG,
                    format='(%(threadName)-9s) %(message)s',)

BUF_SIZE = 10
q = Queue.Queue(BUF_SIZE)

class ProducerThread(threading.Thread):
    def __init__(self, group=None, target=None, name=None,
                 args=(), kwargs=None, verbose=None):
        super(ProducerThread,self).__init__()
        self.target = target
        self.name = name

    def run(self):
        while True:
            if not q.full():
                item = random.randint(1,10)
                q.put(item)
                logging.debug('Putting ' + str(item)  
                              + ' : ' + str(q.qsize()) + ' items in queue')
                time.sleep(random.random())
        return

class ConsumerThread(threading.Thread):
    def __init__(self, group=None, target=None, name=None,
                 args=(), kwargs=None, verbose=None):
        super(ConsumerThread,self).__init__()
        self.target = target
        self.name = name
        return

    def run(self):
        while True:
            if not q.empty():
                item = q.get()
                logging.debug('Getting ' + str(item) 
                              + ' : ' + str(q.qsize()) + ' items in queue')
                time.sleep(random.random())
        return

if __name__ == '__main__':
    
    p = ProducerThread(name='producer')
    c = ConsumerThread(name='consumer')

    p.start()
    time.sleep(2)
    c.start()
    time.sleep(2)      
```


## MessageQueue方案

基于多线程方案，这个问题已经演变成消息中介模式（有些公司喜欢称之为"邮局"），有各种的商业MQ方案可以直接使用。

这里以RabbitMQ开源方案为例，Producer一方向名为队列中发送"Hello World!"内容，而Consumer一方则监听队列，当有内容进入队列时，就执行callback函数来收取并处理内容。发送与收取的动作是异步执行的，互不干扰。

``` py
###### Producer ########

import pika
connection = pika.BlockingConnection(pika.ConnectionParameters(
        host='localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

channel.basic_publish(exchange='',
                      routing_key='hello',
                      body='Hello World!')
print " [x] Sent 'Hello World!'"
connection.close()

```

``` py
####### Consumer ########

import pika
connection = pika.BlockingConnection(pika.ConnectionParameters(
        host='localhost'))
channel = connection.channel()

channel.queue_declare(queue='hello')

print ' [*] Waiting for messages. To exit press CTRL+C'

def callback(ch, method, properties, body):
    print " [x] Received %r" % (body,)

channel.basic_consume(callback,
                      queue='hello',
                      no_ack=True)

channel.start_consuming()

```

# yield与协程


## 何为协程(Coroutine)及yield
python采用了GIL(Global Interpretor Lock，全局解释器锁)，默认所有任务都是在同一进程中执行的。（当然，可以借助多进程多线程来实现并行化。）我们调用一个普通的Python函数时，一般是从函数的第一行代码开始执行，结束于return语句、异常或者函数结束（可以看作隐式的返回None）。一旦函数将控制权交还给调用者，就意味着全部结束。函数中做的所有工作以及保存在局部变量中的数据都将丢失。再次调用这个函数时，一切都将从头创建。

所谓协程(Coroutine)就是在同一进程/线程中，利用生成器(generator)来"同时"执行多个函数(routine)。 

Python的中yield关键字与Coroutine说的是一件事情，先看看yield的基本用法。

任何包含`yield`关键字的函数都会自动成为生成器(generator)对象,里面的代码一般是一个有限或无限循环结构，每当第一次调用该函数时，会执行到yield代码为止并返回本次迭代结果，yield指令起到的是return关键字的作用。然后函数的堆栈会自动冻结(freeze)在这一行。当函数调用者的下一次利用next()或generator.send()或for-in来再次调用该函数时，就会从yield代码的下一行开始，继续执行，再返回下一次迭代结果。通过这种方式，迭代器可以实现无限序列和惰性求值。

看一个用生成器来计算100以内斐波那契数列的例子。我们先用普通递归方式来进行计算。

``` py
a = b = 1
while a < 100:
    a, b = b, a + b
    print a,
```

再来用yield和生成器来计算斐波那契数列，该函数形成一个无限循环的生成器，由函数调用者显式地控制迭代次数。

``` py
def fibonacci():
    a = b = 1
    yield a
    yield b
    while True:
        a, b = b, a+b
        yield b

num = 0
fib = fibonacci()
while num < 100:
    num = next(fib)
    print num,

```

总而言之，生成器（以及yield语句）最初的引入是为了让程序员可以更简单的编写用来产生值的序列的代码。 以前，要实现类似随机数生成器的东西，需要实现一个类或者一个模块，在生成数据的同时保持对每次调用之间状态的跟踪。引入生成器之后，这变得非常简单。
* yield则像是generator函数的返回结果
* yield唯一所做的另一件事就是保存一个generator函数的状态
* generator就是一个特殊类型的迭代器（iterator）
* 和迭代器相似，我们可以通过使用next()来从generator中获取下一个值
* 通过隐式地调用next()来忽略一些值
 


## 用yield实现协程调度的原理

我们现在利用yield关键字会自动冻结函数堆栈的特性，想象一下，假如现在有两个函数f1()和f2(),各自包含yield语句，见下例。主线程先启动f1(), 当f1()执行到yield的时候，暂时返回。这时主线程可以将执行权交给f2()，执行到f2()的yield后，可以再将执行权交给f1()，从而实现了在同一线程中交错执行f1()和f2()。f1()与f2()就是协同执行的程序，故名协程。

我们尝试用yield建立协程，来解决Producer-Consumer问题。

``` py 
# -*- coding: utf-8 -*-
import random
 
def get_data():
    """返回0到9之间的3个随机数，模拟异步操作"""
    return random.sample(range(10), 3)
 
def consume():
    """显示每次传入的整数列表的动态平均值"""
    running_sum = 0
    data_items_seen = 0
    
    while True:
        print('Waiting to consume')
        data = yield
        data_items_seen += len(data)
        running_sum += sum(data)
        print('Consumed, the running average is {}'.format(running_sum / float(data_items_seen)))
 
def produce(consumer):
    """产生序列集合，传递给消费函数（consumer）"""
    while True:
        data = get_data()
        print('Produced {}'.format(data))
        consumer.send(data)
        yield
 
if __name__ == '__main__':
    consumer = consume()
    consumer.send(None) 
    producer = produce(consumer)
 
    for _ in range(10):
        print('Producing...')
        next(producer)
```

下图将控制流形象化，以说明上下文切换如何发生。

[](http://blog.ez2learn.com/wp-content/uploads/2010/07/coroutine_io_diagram.jpg)

    在任何时刻，只有一个协程在运行。


# 异步编程同步化

## 不再需要回调
看一下Python官方的例子，利用一个`@gen.coroutine`装饰器来简化代码编写，原本__调用-回调__两段逻辑，现在被放在了一起，yield充当了回调的入口。这就是__异步编程同步化__！

原始的回调编程模式：
``` py
class AsyncHandler(RequestHandler):
    @asynchronous
    def get(self):
        http_client = AsyncHTTPClient()
        http_client.fetch("http://example.com",
                          callback=self.on_fetch)

    def on_fetch(self, response):
        do_something_with_response(response)
        self.render("template.html")
```

同步化编程后的结果：

``` py
class GenAsyncHandler(RequestHandler):
    @gen.coroutine
    def get(self):
        http_client = AsyncHTTPClient()
        response = yield http_client.fetch("http://example.com")
        do_something_with_response(response)
        self.render("template.html")
```


关于这个装饰器的实现方式，可以参见<http://my.oschina.net/u/877348/blog/184058>


## Gevent与Greenlet库
看了上述代码，你是不是觉得利用协程就可以将并发编程全部同步化了？错！
仔细想想，即使用了协程，同一时间仍然只能有一段代码得到执行，此时如果有同步的I/O任务，则仍会存在阻塞想象。除非...除非将I/O任务自动并发掉，才有可能真正利用协程来将大量异步并发任务同步化！注意这里的http_client是异步网络库，非同步阻塞库。一般是需要回调，但利用协程对get()函数同步化以后，当执行到yield时，相当于发出了多个网络请求，然后挂起这个get()函数，其他协程将得到调度。当异步网络请求都已返回且协程调度有空闲时，会调用get.send()，继续这个协程，以同步化编程的方式继续完成原先放在回调函数中的逻辑。上例中网络请求如果采用普通的urllib.urlopen()就不行了。

慢着，如果urllib.urlopen()能够异步执行，那不就行了？

这就是Greenlet库所做的，它是以C扩展模块形式接入Python的轻量级协程，将一些原本同步运行的网络库以mockey_patch的方式进行了重写。Greenlets全部运行在主程序操作系统进程的内部，但它们被协作式地调度。

而Gevent库则是基于Greenlet，实现了协程调度功能。将多个函数spawn为协程，然后join到一起，如此简单！

看一个Gevent的官方例子：


``` py
import gevent.monkey
gevent.monkey.patch_socket()

import gevent
import urllib2
import simplejson as json

def fetch(pid):
    response = urllib2.urlopen('http://json-time.appspot.com/time.json')
    result = response.read()
    json_result = json.loads(result)
    datetime = json_result['datetime']

    print('Process %s: %s' % (pid, datetime))
    return json_result['datetime']

def synchronous():
    for i in range(1,10):
        fetch(i)

def asynchronous():
    threads = []
    for i in range(1,10):
        threads.append(gevent.spawn(fetch, i))
    gevent.joinall(threads)

print('Synchronous:')
synchronous()

print('Asynchronous:')
asynchronous()
```

## multiprocessing.dummy.ThreadPool库
实现异步编程同步化还有一个方法，就是利用的map()函数。这个函数我们并不陌生，它可以在一个序列上实现某个函数之间的映射。

``` py
results = map(urllib2.urlopen, ['http://www.yahoo.com', 'http://www.reddit.com'])
```

上述代码对会依次访问每个url，不过因为只有一个进程，后一个urlopen仍然需要等待前一个urlopen完成后才会进行，仍然是一种串行的方式。但是，只要借助正确的库，map()也可以轻松实现并行化操作，那就是`multiprocessing`库。

这个库以及其鲜为人知的子库`multiprocessing.dummy`，一个用于多进程，一个用于多线程。后者提供改良的map()函数，可以自动将多个异步任务，分配到多个线程上，编程人员无需关注，也就自然地把异步编程转为了同步编程的风格。IO 密集型任务选择multiprocessing.dummy，CPU 密集型任务选择multiprocessing。

前述那个教科书式的例子，可以改写为
``` py
import urllib2 
from multiprocessing.dummy import Pool ThreadPool
urls = [ 'http://www.python.org', 'http://www.python.org/about/', 'http://www.python.org/doc/', 'http://www.python.org/download/']
# Make the Pool of workers 
pool = ThreadPool() 
# Open the urls in their own threads and return the results 
results = pool.map(urllib2.urlopen, urls) 
#close the pool and wait for the work to finish 
pool.close()
pool.join()
```


关于`map()`函数和`yield`关键字的解释，请参考 [@申导](http://www.jackyshen.com) 的另一篇文章[《Python函数式编程》](http://www.jackyshen.com/2014/10/02/functional-programming-in-Python/)

# 参考：

* <http://my.oschina.net/u/877348/blog/184058>
* <https://www.rabbitmq.com/tutorials/tutorial-one-python.html>
* <http://www.jackyshen.com/2014/10/02/functional-programming-in-Python>
* <http://tornado.readthedocs.org/en/latest/gen.html>
* <http://blog.ez2learn.com/2010/07/17/talk-about-coroutine-and-gevent/>
* <http://www.bogotobogo.com/python/Multithread/python_multithreading_Synchronization_Producer_Consumer_using_Queue.php>