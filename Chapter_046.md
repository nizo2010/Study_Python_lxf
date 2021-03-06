# Chapter_046 多进程

## fork()

Unix/Linux操作系统平台提供了一个fork()系统调用。Mac系统是基于BSD（Unix的一种）内核，因此Mac下也有这个函数。

普通的函数调用，调用一次，返回一次。

fork()调用一次，返回两次，操作系统自动把当前进程（称为父进程）复制一份（称为子进程），然后分别在父进程和子进程内返回。

1. 首先父进程返回，返回的是子进程ID

2. 然后子进程返回，返回的永远是0

3. 因为一个父进程可以fork出很多子进程，所以父进程要记下每个子进程的ID，而子进程可以调用getppid()返回其父进程的ID

```python
nte-env57-dc54-zbk:/zhangning # cat multiprocess.py
#! /usr/bin/python
import os

print('1---Process (%s) start...' % os.getpid())                  # os.getpid()返回当前进程（父进程）ID

pid = os.fork()                                                   # 先调用父进程返回子进程ID，再调用子进程返回0
                                                                  # 所以先执行else语句块，再执行if语句块

if pid == 0:
    print('2---pid =',pid)                                        # 此时调用子进程，返回pid是0
    print('3---os.getpid =',os.getpid())                          # os.getpid()返回子进程ID
    print('4---os.getppid() =',os.getppid())                      # os.getppid()返回父进程ID
    print('5---I am child process (%s) and my parent is %s.' % (os.getpid(), os.getppid()))
else:
    print('6---pid =',pid)                                        # 此时pid是子进程ID
    print('7---os.getpid =',os.getpid())                          # 由于此时是父进程调用，os.getpid()返回父进程ID
    print('8---os.getppid() =',os.getppid())                      
    print('9---I (%s) just created a child process (%s).' % (os.getpid(), pid))

### 执行结果
nte-env57-dc54-zbk:/zhangning # python multiprocess.py
1---Process (139762) start...
6---pid =139763
7---os.getpid =139762
8---os.getppid() =182675
9---I (139762) just created a child process (139763).
2---pid =0
3---os.getpid =139763
4---os.getppid() =139762
5---I am child process (139763) and my parent is 139762.
```

> 因为Windows下没有fork调用，以上程序代码无法在Windows平台执行

## multiprocessing模块

multiprocessing模块是跨平台版本的多进程模块。提供一个Process类来代表一个进程对象。

```python
from multiprocessing import Process
import os

# 子进程要执行的代码
def run_proc(name):
    print('Run child process %s (%s)...' % (name, os.getpid()))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Process(target=run_proc, args=('test',))          # Process类实例
    print('Child process will start.')
    p.start()                                             # start()方法启动进程
    p.join()                                              # join()方法等待子进程结束后继续往下进行，注释掉后可能会先执行后续语句
    print('Child process end.')
    
### 运行结果
Parent process 8884.
Child process will start.
Run child process test (9808)...
Child process end.

### 注释掉p.join()后的运行结果
Parent process 300.
Child process will start.
Child process end.
Run child process test (7032)...
```


## 进程池 Pool

如果要启动大量子进程，可以使用进程池的方式批量创建子进程

```python
from multiprocessing import Pool
import os, time, random

def long_time_task(name):
    print('Run task %s (%s)...' % (name, os.getpid()))
    start = time.time()                                                 # 子进程调用开始时间
    time.sleep(random.random() * 3)                                     # 随机等待0~3秒
    end = time.time()                                                   # 子进程调用结束时间
    print('Task %s runs %0.2f seconds.' % (name, (end - start)))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Pool(4)                                                         # 进程池里有4个进程，即最多同时执行4个进程
    for i in range(5):                                                  # 5个任务
        p.apply_async(long_time_task, args=(i,))
    print('Waiting for all subprocesses done...')
    p.close()                                                           # join()调用前必须先调用close()，表示不能继续添加新的Process
    p.join()
    print('All subprocesses done.')

### 运行结果
Parent process 8336.
Waiting for all subprocesses done...
Run task 0 (7904)...
Run task 1 (4232)...
Run task 2 (10772)...
Run task 3 (696)...
Task 3 runs 0.14 seconds.                                 # Pool里有4个子进程，有5个任务
Run task 4 (696)...                                       # 因此先执行4个任务，执行完一个任务后，才有进程能够执行第五个任务
Task 4 runs 1.32 seconds.
Task 1 runs 1.76 seconds.
Task 0 runs 2.47 seconds.
Task 2 runs 2.45 seconds.
All subprocesses done.
```

> 对Pool对象调用join()方法会等待所有子进程执行完毕，调用join()之前必须先调用close()，调用close()之后就不能继续添加新的Process了。

请注意输出的结果，task 0，1，2，3是立刻执行的，而task 4要等待前面某个task完成后才执行，这是因为Pool的默认大小在我的电脑上是4，因此，最多同时执行4个进程。这是Pool有意设计的限制，并不是操作系统的限制。如果改成：

> p = Pool(5)

就可以同时跑5个进程。

由于Pool的默认大小是CPU的核数，如果你不幸拥有8核CPU，你要提交至少9个子进程才能看到上面的等待效果。


## 子进程

很多时候，子进程并不是自身，而是一个外部进程。我们创建了子进程后，还需要控制子进程的输入和输出。

subprocess模块可以让我们非常方便地启动一个子进程，然后控制其输入和输出。

下面的例子演示了如何在Python代码中运行命令

> nslookup www.python.org

这和命令行直接运行的效果是一样的：

```python
import subprocess

print('$ nslookup www.python.org')

r = subprocess.call(['nslookup', 'www.python.org'])

print('Exit code:', r)



###运行结果：
$ nslookup www.python.org
Server:  lg-ns2.huawei.com
Address:  10.72.55.103

Non-authoritative answer:
Name:    dualstack.python.map.fastly.net
Addresses:  2a04:4e42:36::223
          151.101.72.223
Aliases:  www.python.org

Exit code: 0
```

如果子进程还需要输入，则可以通过communicate()方法输入：

```python
import subprocess

print('$ nslookup')

p = subprocess.Popen(['nslookup'], stdin=subprocess.PIPE,stdout=subprocess.PIPE,stderr=subprocess.PIPE)

output, err = p.communicate(b'set q=mx\npython.org\nexit\n')

print(output.decode('utf-8'))

print('Exit code:', p.returncode)

```

上面的代码相当于在命令行执行命令nslookup，然后手动输入：

> set q=mx
>
> python.org
>
> exit

运行结果如下：

```python
$ nslookup
Default Server:  lg-ns2.huawei.com
Address:  10.72.55.103

> > Server:  lg-ns2.huawei.com
Address:  10.72.55.103

python.org      MX preference = 50, mail exchanger = mail.python.org

python.org      nameserver = ns4.p11.dynect.net
python.org      nameserver = ns3.p11.dynect.net
python.org      nameserver = ns1.p11.dynect.net
python.org      nameserver = ns2.p11.dynect.net
mail.python.org internet address = 188.166.95.178
ns1.p11.dynect.net      internet address = 208.78.70.11
ns2.p11.dynect.net      internet address = 204.13.250.11
ns3.p11.dynect.net      internet address = 208.78.71.11
ns4.p11.dynect.net      internet address = 204.13.251.11
mail.python.org AAAA IPv6 address = 2a03:b0c0:2:d0::71:1
>
Exit code: 0
```


## 进程间通信

Process之间肯定是需要通信的，操作系统提供了很多机制来实现进程间的通信。Python的multiprocessing模块包装了底层的机制，提供了Queue、Pipes等多种方式来交换数据。

我们以Queue为例，在父进程中创建两个子进程，一个往Queue里写数据，一个从Queue里读数据：

```python
from multiprocessing import Process, Queue
import os, time, random

# 写数据进程执行的代码:
def write(q):
    print('Process to write: %s' % os.getpid())
    for value in ['A', 'B', 'C']:
        print('Put %s to queue...' % value)
        q.put(value)
        time.sleep(random.random())

# 读数据进程执行的代码:
def read(q):
    print('Process to read: %s' % os.getpid())
    while True:
        value = q.get(True)
        print('Get %s from queue.' % value)

if __name__=='__main__':
    # 父进程创建Queue，并传给各个子进程：
    q = Queue()
    pw = Process(target=write, args=(q,))
    pr = Process(target=read, args=(q,))
    # 启动子进程pw，写入:
    pw.start()
    # 启动子进程pr，读取:
    pr.start()
    # 等待pw结束:
    pw.join()
    # pr进程里是死循环，无法等待其结束，只能强行终止:
    pr.terminate()

### 运行结果如下：
Process to write: 50563
Put A to queue...
Process to read: 50564
Get A from queue.
Put B to queue...
Get B from queue.
Put C to queue...
Get C from queue.
```

在Unix/Linux下，multiprocessing模块封装了fork()调用，使我们不需要关注fork()的细节。由于Windows没有fork调用，因此，multiprocessing需要“模拟”出fork的效果，父进程所有Python对象都必须通过pickle序列化再传到子进程去，所有，如果multiprocessing在Windows下调用失败了，要先考虑是不是pickle失败了。


## apply方法与apply_async方法

### apply方法是阻塞的

意思就是等待当前子进程执行完毕后，在执行下一个进程。

例如：

有三个进程0，1，2。等待子进程0执行完毕后，在执行子进程1，然后子进程2，最后回到主进程执行主进程剩余部分，就像上面的执行结果一样。

这样好像跟单进程串行执行没什么区别了。

### apply_async 是异步非阻塞的。

意思就是：不用等待当前进程执行完毕，随时根据系统调度来进行进程切换。

完全没有等待子进程执行完毕，主进程就已经执行完毕，并退出程序。

为什么会这样呢？

因为进程的切换是操作系统来控制的，抢占式的切换模式。

我们首先运行的是主进程，cpu运行很快啊，这短短的几行代码，完全没有给操作系统进程切换的机会，主进程就运行完毕了，整个程序结束。子进程完全没有机会切换到程序就已经结束了。

> apply是阻塞式的。
>
> 首先主进程开始运行，碰到子进程，操作系统切换到子进程，等待子进程运行结束后，在切换到另外一个子进程，直到所有子进程运行完毕。然后在切换到主进程，运行剩余的部分。
>
> apply_async是异步非阻塞式的。
>
> 首先主进程开始运行，碰到子进程后，主进程说：让我先运行个够，等到操作系统进行进程切换的时候，在交给子进程运行。以为我们的程序太短，然而还没等到操作系统进行进程切换，主进程就运行完毕了。

```python
from multiprocessing import Pool
import os, time, random

def long_time_task(name):
    print('Run task %s (%s)...' % (name, os.getpid()))
    start = time.time()                                                 
    time.sleep(random.random() * 3)                                     
    end = time.time()                                                   
    print('Task %s runs %0.2f seconds.' % (name, (end - start)))

if __name__=='__main__':
    print('Parent process %s.' % os.getpid())
    p = Pool(4)                                                         
    for i in range(5):                                                  
        p.apply(long_time_task, args=(i,))            # 这里使用apply()方法，阻塞式
    print('Waiting for all subprocesses done...')     # 这里就不需要调用p.close()和p.join()了
    print('All subprocesses done.')

### 运行结果
Parent process 7976.
Run task 0 (7208)...
Task 0 runs 2.75 seconds.
Run task 1 (8892)...
Task 1 runs 1.48 seconds.
Run task 2 (7664)...
Task 2 runs 1.24 seconds.
Run task 3 (7980)...
Task 3 runs 0.23 seconds.
Run task 4 (7208)...
Task 4 runs 0.68 seconds.
Waiting for all subprocesses done...
All subprocesses done.
```


## 链接

上一节 [Chapter_045 进程和线程](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_045.md "Chapter_045 进程和线程")

下一节 [Chapter_047 多线程](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_047.md "Chapter_047 多线程")
