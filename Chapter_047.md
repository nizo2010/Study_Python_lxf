# Chapter_047 多线程

线程是操作系统直接支持的执行单元，因此高级语言通常都内置多线程的支持，Python也不例外，并且Python的线程是真正的Posix Thread，而不是模拟出来的结果。

## threading模块

Python的标准库提供了两个模块：\_thread和threading，\_thread是低级模块，threading是高级模块，对\_thread进行封装。因此我们直接使用threading模块即可。

启动一个线程就是把一个函数传入并穿件Thread实例，然后调用start()开始执行：

```python
import time,threading

def loop():
	print('thread %s is running...' % threading.current_thread().name)
	n = 0
	while n < 5:
		n = n + 1
		print('thread %s >>> %s' % (threading.current_thread().name, n))
		time.sleep(1)
	print('thread %s ended.' % threading.current_thread().name)
	
print('thread %s is running...' % threading.current_thread().name)          # 主线程，name永远为MainThread
t = threading.Thread(target=loop, name='LoopThread')                        # 子线程创建，name指定为LoopThread

t.start()
t.join()
print('thread %s ended.' % threading.current_thread().name)

### 运行结果
thread MainThread is running...
thread LoopThread is running...
thread LoopThread >>> 1
thread LoopThread >>> 2
thread LoopThread >>> 3
thread LoopThread >>> 4
thread LoopThread >>> 5
thread LoopThread ended.
thread MainThread ended.
```

由于任何进程默认就会启动一个线程，我们把该线程称为主线程，主线程又可以启动新的线程，Python的threading模块有一个current_thread()函数，它永远返回当前线程的实例。

主线程实例的名字name永远叫MainThread，子线程的名字name在创建的时候指定，这里用LoopThread命名子线程，默认为Thread-1,Thread-2,...

线程的名字仅仅在打印时用来显示区分线程，没有其他意义。


## Lock

多线程和多进程最大的不同在于，多进程中，同一个变量，各自有一份拷贝存在于每个进程中，互不影响，而多线程中，所有变量都由所有线程共享，所以，任何一个变量都可以被任何一个线程修改，因此，线程之间共享数据最大的危险在于多个线程同时改一个变量，把内容给改乱了。

举一个例子：

```python
import time,threading

balance = 0

def change_it(n):
	global balance
	balance = balance + n
	balance = balance - n
	
def run_thread(n):
	for i in range(1000000):
		change_it(n)
		
t1 = threading.Thread(target=run_thread, args=(5,))
t2 = threading.Thread(target=run_thread, args=(8,))

t1.start()
t2.start()

t1.join()
t2.join()

print(balance)

### 运行结果：
13
```

解释一下这个代码运行，首先定义了一个共享变量balance，初始值是0，然后启动两个线程t1和t2，先存后取，理论上最终balance结果应该是0。

但是由于线程的调度是由操作系统决定的，当t1、t2交替执行时，只要循环次数足够多，balance的结果就不一定是0。

为什么呢？

原因是因为高级语言的一条语句在CPU执行时是由若干条语句组合而成的，即便是一条简单的计算语句：

```python
balance = balance + n
```

也会分为两步执行：

1. 计算balance + n，存入临时变量中；

2. 将临时变量的值赋给balance。

也就是可以看成：

```python
x = balance + n
balance = x
```

由于x是临时的局部变量，两个线程各自都有自己的x，当代码正常执行时是这样的：

```python
初始值 balance = 0

t1: x1 = balance + 5 # x1 = 0 + 5 = 5
t1: balance = x1     # balance = 5
t1: x1 = balance - 5 # x1 = 5 - 5 = 0
t1: balance = x1     # balance = 0

t2: x2 = balance + 8 # x2 = 0 + 8 = 8
t2: balance = x2     # balance = 8
t2: x2 = balance - 8 # x2 = 8 - 8 = 0
t2: balance = x2     # balance = 0

结果 balance = 0
```

但是线程t1和t2是交替运行的，如果操作系统以如下的顺序执行，就是这样的：

```python
初始值 balance = 0

t1: x1 = balance + 5  # x1 = 0 + 5 = 5

t2: x2 = balance + 8  # x2 = 0 + 8 = 8
t2: balance = x2      # balance = 8

t1: balance = x1      # balance = 5
t1: x1 = balance - 5  # x1 = 5 - 5 = 0
t1: balance = x1      # balance = 0

t2: x2 = balance - 8  # x2 = 0 - 8 = -8
t2: balance = x2   # balance = -8

结果 balance = -8
```

究其原因，是因为修改balance需要多条语句，而执行这几条语句时，线程可能中断，从而导致多个线程把同一个对象的内容改乱了。

两个线程同时一存一取，就可能导致余额不对，你肯定不希望你的银行存款莫名其妙地变成了负数，所以，我们必须确保一个线程在修改balance的时候，别的线程一定不能改。

如果我们要确保balance计算正确，就要给change_it()上一把锁，当某个线程开始执行change_it()时，我们说，该线程因为获得了锁，因此其他线程不能同时执行change_it()，只能等待，直到锁被释放后，获得该锁以后才能改。由于锁只有一个，无论多少线程，同一时刻最多只有一个线程持有该锁，所以，不会造成修改的冲突。创建一个锁就是通过threading.Lock()来实现：

```python
import time,threading

balance = 0
lock = threading.Lock()

def change_it(n):
	global balance
	balance = balance + n
	balance = balance - n
	
def run_thread(n):
	for i in range(1000000):
		lock.acquire()
		try:
			change_it(n)
		finally:
			lock.release()
		
t1 = threading.Thread(target=run_thread, args=(5,))
t2 = threading.Thread(target=run_thread, args=(8,))

t1.start()
t2.start()

t1.join()
t2.join()

print(balance)

### 运行结果：
0
```

当多个线程同时执行lock.acquire()时，只有一个线程能成功地获取锁，然后继续执行代码，其他线程就继续等待直到获得锁为止。

获得锁的线程用完后一定要释放锁，否则那些苦苦等待锁的线程将永远等待下去，成为死线程。所以我们用try...finally来确保锁一定会被释放。

锁的好处就是确保了某段关键代码只能由一个线程从头到尾完整地执行，坏处当然也很多，首先是阻止了多线程并发执行，包含锁的某段代码实际上只能以单线程模式执行，效率就大大地下降了。其次，由于可以存在多个锁，不同的线程持有不同的锁，并试图获取对方持有的锁时，可能会造成死锁，导致多个线程全部挂起，既不能执行，也无法结束，只能靠操作系统强制终止。


## 多核CPU

如果你不幸拥有一个多核CPU，你肯定在想，多核应该可以同时执行多个线程。

如果写一个死循环的话，会出现什么情况呢？

打开Mac OS X的Activity Monitor，或者Windows的Task Manager，都可以监控某个进程的CPU使用率。

我们可以监控到一个死循环线程会100%占用一个CPU。

如果有两个死循环线程，在多核CPU中，可以监控到会占用200%的CPU，也就是占用两个CPU核心。

要想把N核CPU的核心全部跑满，就必须启动N个死循环线程。

试试用Python写个死循环：

```python
import threading, multiprocessing

def loop():
	x = 0
	while True:
		x = x ^ 1
		
for i in range(multiprocessing.cpu_count()):
	t = threading.Thread(target=loop)
	t.start()
```

启动与CPU核心数量相同的N个线程，在4核CPU上可以监控到CPU占用率仅有102%，也就是仅使用了一核。

但是用C、C++或Java来改写相同的死循环，直接可以把全部核心跑满，4核就跑到400%，8核就跑到800%，为什么Python不行呢？

因为Python的线程虽然是真正的线程，但解释器执行代码时，有一个GIL锁：Global Interpreter Lock，任何Python线程执行前，必须先获得GIL锁，然后，每执行100条字节码，解释器就自动释放GIL锁，让别的线程有机会执行。这个GIL全局锁实际上把所有线程的执行代码都给上了锁，所以，多线程在Python中只能交替执行，即使100个线程跑在100核CPU上，也只能用到1个核。

GIL是Python解释器设计的历史遗留问题，通常我们用的解释器是官方实现的CPython，要真正利用多核，除非重写一个不带GIL的解释器。

所以，在Python中，可以使用多线程，但不要指望能有效利用多核。如果一定要通过多线程利用多核，那只能通过C扩展来实现，不过这样就失去了Python简单易用的特点。

不过，也不用过于担心，Python虽然不能利用多线程实现多核任务，但可以通过多进程实现多核任务。多个Python进程有各自独立的GIL锁，互不影响。


## 小结

1. 多线程编程，模型复杂，容易发生冲突，必须用锁加以隔离，同时，又要小心死锁的发生。

2. Python解释器由于设计时有GIL全局锁，导致了多线程无法利用多核。多线程的并发在Python中就是一个美丽的梦。


## 课后练习

1、多进程多线程死循环程序：

```python
import multiprocessing
from multiprocessing import Pool, Process
import threading

def loop(i):
    x = 0
    print('Thread - ',i)
    while True:
        x = x ^ 1

def proc(i, cpu_cout):
    print('Process: ',i)
    for i in range(cpu_cout*2):
        t = threading.Thread(target=loop, args=(i,))
        t.start()

if __name__ == '__main__':
    cpu_cout = multiprocessing.cpu_count()
    p = Pool(cpu_cout)
    for i in range(cpu_cout):
        p.apply_async(proc,args=(i, cpu_cout))
    p.close()
    p.join()
```

## 参考
[搞定python多线程和多进程](http://www.cnblogs.com/whatisfantasy/p/6440585.html "搞定python多线程和多进程")

[理解python中多线程](http://python.jobbole.com/86909/ "理解python中多线程")


## 链接

上一节 [Chapter_046 多进程](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_046.md "Chapter_046 多进程")

下一节 [Chapter_048 ThreadLocal](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_048.md "Chapter_048 ThreadLocal")
