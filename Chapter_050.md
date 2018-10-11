# Chapter_050 分布式进程

在Thread和Process中，应当优选Process，因为Process更稳定，而且Process可以分布到多台机器上，而Thread最多只能分布在同一台机器的多个CPU上。

Python的multiprocessing模块不但支持多进程，其中的managers子模块还支持把多进程分布到多台机器上。一个服务进程可以作为调度者，依靠网络通信将任务分布到其他多个进程中。由于managers子模块封装得很好，不必了解网络通信的细节，就可以很容易地编写分布式多进程程序。

## 例子

如果现在已经有一个通过Queue通信的多进程程序在同一台机器上运行，由于处理任务的进程任务繁重，希望把发送任务的进程和处理任务的进程分布到两台机器上。

原有的Queue可以继续使用，通过managers模块把Queue通过网络暴露出去，就可以让其他机器的进程访问Queue了。


### 服务进程

服务进程负责启动Queue，把Queue注册到网络上，然后往Queue里面写入任务：

```python
# task_master.py

import random, time, queue
from multiprocessing.managers import BaseManager

# 发送任务的队列:
task_queue = queue.Queue()
# 接收结果的队列:
result_queue = queue.Queue()

# 从BaseManager继承的QueueManager:
class QueueManager(BaseManager):
    pass

# 把两个Queue都注册到网络上, callable参数关联了Queue对象:
QueueManager.register('get_task_queue', callable=lambda: task_queue)
QueueManager.register('get_result_queue', callable=lambda: result_queue)
# 绑定端口5000, 设置验证码'abc':
manager = QueueManager(address=('127.0.0.1', 5000), authkey=b'abc')
# 启动Queue:
manager.start()
# 获得通过网络访问的Queue对象:
task = manager.get_task_queue()
result = manager.get_result_queue()
# 放几个任务进去:
for i in range(10):
    n = random.randint(0, 10000)
    print('Put task %d...' % n)
    task.put(n)
# 从result队列读取结果:
print('Try get results...')
for i in range(10):
    r = result.get(timeout=10)
    print('Result: %s' % r)
# 关闭:
manager.shutdown()
print('master exit.')
```

请注意，当我们在一台机器上写多进程程序时，创建的Queue可以直接拿来用，但是在分布式多进程环境下，添加任务到Queue不可以直接对原始的task_queue进行操作，那样就绕过了QueueManager的封装，必须通过manager.get_task_queue()获得的Queue接口添加。


### 启动任务进程

在另外一台机器上启动任务进程（本机上启动也是可以的）：

```python
# task_worker.py

import time, sys, queue
from multiprocessing.managers import BaseManager

# 创建类似的QueueManager:
class QueueManager(BaseManager):
    pass

# 由于这个QueueManager只从网络上获取Queue，所以注册时只提供名字:
QueueManager.register('get_task_queue')
QueueManager.register('get_result_queue')

# 连接到服务器，也就是运行task_master.py的机器:
server_addr = '127.0.0.1'
print('Connect to server %s...' % server_addr)
# 端口和验证码注意保持与task_master.py设置的完全一致:
m = QueueManager(address=(server_addr, 5000), authkey=b'abc')
# 从网络连接:
m.connect()
# 获取Queue的对象:
task = m.get_task_queue()
result = m.get_result_queue()
# 从task队列取任务,并把结果写入result队列:
for i in range(10):
    try:
        n = task.get(timeout=1)
        print('run task %d * %d...' % (n, n))
        r = '%d * %d = %d' % (n, n, n*n)
        time.sleep(1)
        result.put(r)
    except Queue.Empty:
        print('task queue is empty.')
# 处理结束:
print('worker exit.')
```

任务进程要通过网络连接到服务进程，所以要指定服务进程的IP地址。


### 验证效果

先启动task_master.py服务进程：

```python
C:\Users\user.HSH1000024790\Desktop\Py_Project>python task_master.py
Traceback (most recent call last):
  File "task_master.py", line 20, in <module>
    manager.start()
  File "C:\Python\Python36\lib\multiprocessing\managers.py", line 513, in start
    self._process.start()
  File "C:\Python\Python36\lib\multiprocessing\process.py", line 105, in start
    self._popen = self._Popen(self)
  File "C:\Python\Python36\lib\multiprocessing\context.py", line 322, in _Popen
    return Popen(process_obj)
  File "C:\Python\Python36\lib\multiprocessing\popen_spawn_win32.py", line 65, i
n __init__
    reduction.dump(process_obj, to_child)
  File "C:\Python\Python36\lib\multiprocessing\reduction.py", line 60, in dump
    ForkingPickler(file, protocol).dump(obj)
_pickle.PicklingError: Can't pickle <function <lambda> at 0x00000000003E1E18>: a
ttribute lookup <lambda> on __main__ failed

C:\Users\user.HSH1000024790\Desktop\Py_Project>Traceback (most recent call last)
:
  File "<string>", line 1, in <module>
  File "C:\Python\Python36\lib\multiprocessing\spawn.py", line 99, in spawn_main

    new_handle = reduction.steal_handle(parent_pid, pipe_handle)
  File "C:\Python\Python36\lib\multiprocessing\reduction.py", line 82, in steal_
handle
    _winapi.PROCESS_DUP_HANDLE, False, source_pid)
OSError: [WinError 87] The parameter is incorrect
```

报错了！

> 总结如下：
> 
> 1) linux下创建进程使用fork()，windows平台下创建进程使用CreateProccess()。
> 
> 2) fork()调用之后，没有写操作之前，与父进程共享一份内存，并没有真正拥有所谓多进程的”私有内存”。而CreateProccess()每次执行之后,都确保子进程拥有新的内存空间。
> 
> 3) 这样设计的原因可以从linux和windows多进程的应用场景的差别来理解。fork诞生于无线程时代，因此fork()之后不exec的话，作用与多线程非常相似，可以理解为没有线程概念下的一种解决方案。而CreateProccess则更像创建兄弟进程，创建的进程都保证独立存在。
> 
> 4) 这种差别直接体现在multiprocessing模块当中，在不同平台下使用不同的方法创建例子中的manager进程。在windows下通过将父进程的环境进行序列化存储之后，再传入createProccess当中。
> 
> 5) 问题在于pickling序列化中对匿名函数的不支持，导致创建进程失败。 
> 
> 6) 因此把匿名函数用函数替代即可解决。


### 更新服务进程

根据上述的原因，修改服务进程task_master.py如下：

```python
#task_master.py
import random, time, queue

from multiprocessing.managers import BaseManager

task_queue = queue.Queue()

result_queue = queue.Queue()

#替代原来的匿名函数
def return_task_queue():
    global task_queue
    return task_queue
#替代原来的匿名函数
def return_result_queue():
    global result_queue
    return result_queue


class QueueManager(BaseManager):
    pass

#加入main判断
if __name__ == '__main__':
    #callable参数指定函数
    QueueManager.register('get_task_queue',callable=return_task_queue)
    #callable参数指定函数
    QueueManager.register('get_result_queue',callable=return_result_queue)

    manager = QueueManager(address=('127.0.0.1',34512), authkey=b'abc')

    manager.start()

    task = manager.get_task_queue()
    result = manager.get_result_queue()

    for i in range(10):
        n = random.randint(0, 10000)
        print('Put task %d...' % n)
        task.put(n)

    print('Try get result...')
    for i in range(10):
        r = result.get(timeout=10)
        print('Result: %s' %r)

    manager.shutdown()
    print('master exit.')
```


## 再次验证

运行task_master.py，启动服务进程：

```python
C:\Users\user.HSH1000024790\Desktop\Py_Project>python task_master.py
Put task 5275...
Put task 8138...
Put task 6851...
Put task 9747...
Put task 6201...
Put task 6868...
Put task 4648...
Put task 3669...
Put task 508...
Put task 4896...
Try get results...
```

task_master.py进程发送完任务后，开始等待result队列的结果。现在启动task_worker.py进程

```python
C:\Users\user.HSH1000024790\Desktop\Py_Project>python task_worker.py
Connect to server 127.0.0.1...
run task 5275 * 5275...
run task 8138 * 8138...
run task 6851 * 6851...
run task 9747 * 9747...
run task 6201 * 6201...
run task 6868 * 6868...
run task 4648 * 4648...
run task 3669 * 3669...
run task 508 * 508...
run task 4896 * 4896...
worker exit.
```

同时之前运行的task_master.py进程中会继续打印出结果：

```python
C:\Users\user.HSH1000024790\Desktop\Py_Project>python task_master.py
Put task 5275...
Put task 8138...
Put task 6851...
Put task 9747...
Put task 6201...
Put task 6868...
Put task 4648...
Put task 3669...
Put task 508...
Put task 4896...
Try get results...
Result: 5275 * 5275 = 27825625
Result: 8138 * 8138 = 66227044
Result: 6851 * 6851 = 46936201
Result: 9747 * 9747 = 95004009
Result: 6201 * 6201 = 38452401
Result: 6868 * 6868 = 47169424
Result: 4648 * 4648 = 21603904
Result: 3669 * 3669 = 13461561
Result: 508 * 508 = 258064
Result: 4896 * 4896 = 23970816
master exit.
```

这个简单的Master/Worker模型有什么用？其实这就是一个简单但真正的分布式计算，把代码稍加改造，启动多个worker，就可以把任务分布到几台甚至几十台机器上，比如把计算n\*n的代码换成发送邮件，就实现了邮件队列的异步发送。

Queue对象存储在哪？注意到task_worker.py中根本没有创建Queue的代码，所以，Queue对象存储在task_master.py进程中：

> task_master.py                              |         task_worker.py
> 
>   task = manager.get_task_queue()           |           task = manager.get_task_queue()
>   
>   result = manager.get_result_queue()       |           result = manager.get_result_queue()
>   
>                 |                           |                        |
>                 
>                 |                           |                        |
> 
>                 V                           |                        |
>        
>        QueueManager                         |                        |
>          
>                                      <------+------------------------      
>          
>          task_queue     result_queue        |
>          
>                                             |
>                                             
>                                          Network

而Queue之所以能通过网络访问，就是通过QueueManager实现的。由于QueueManager管理的不止一个Queue，所以，要给每个Queue的网络调用接口起个名字，比如get_task_queue。

authkey有什么用？这是为了保证两台机器正常通信，不被其他机器恶意干扰。如果task_worker.py的authkey和task_master.py的authkey不一致，肯定连接不上。


## 小结

Python的分布式进程接口简单，封装良好，适合需要把繁重任务分布到多台机器的环境下。

注意Queue的作用是用来传递任务和接收结果，每个任务的描述数据量要尽量小。比如发送一个处理日志文件的任务，就不要发送几百兆的日志文件本身，而是发送日志文件存放的完整路径，由Worker进程再去共享的磁盘上读取文件。


## 链接

上一节 [Chapter_049 进程 vs 线程](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_049.md "Chapter_049 进程 vs 线程")

下一节 [Chapter_051 正则表达式](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_051.md "Chapter_051 正则表达式")
