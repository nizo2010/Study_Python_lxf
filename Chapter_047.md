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

