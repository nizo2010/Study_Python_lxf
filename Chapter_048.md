# Chapter_048 ThreadLocal

在多线程环境下，每个线程都有自己的数据。一个线程使用自己的局部变量比使用全局变量好，因为局部变量只有线程自己能看见，不会影响其他线程，而全局变量的修改必须加锁。

但是局部变量也有问题，就是在函数调用的时候，传递起来很麻烦：

```python
def process_student(name):
    std = Student(name)
    # std是局部变量，但是每个函数都要用它，因此必须传进去：
    do_task_1(std)
    do_task_2(std)

def do_task_1(std):
    do_subtask_1(std)
    do_subtask_2(std)

def do_task_2(std):
    do_subtask_2(std)
    do_subtask_2(std)
```

每个函数一层一层调用都这样传递参数就很麻烦。当然是要全局变量也不行，因为每个线程处理不同的Student对象，不能共享。

如果用一个全局dict存放所有的Student对象，然后以thread自身作为key获取线程对应的Student对象：

```python
global_dict = {}

def std_thread(name):
    std = Student(name)
    # 把std放到全局变量global_dict中：
    global_dict[threading.current_thread()] = std
    do_task_1()
    do_task_2()

def do_task_1():
    # 不传入std，而是根据当前线程查找：
    std = global_dict[threading.current_thread()]
    ...

def do_task_2():
    # 任何函数都可以查找出当前线程的std变量：
    std = global_dict[threading.current_thread()]
    ...
```

上述方法是可以的，其最大的优点是消除了std对象在每层函数中的传递问题，但是，代码结果不是那么优美（每个函数都要去获取std的代码）。

因此引出了ThreadLocal，不用查找dict，ThreadLocal会帮我们自动做这件事情：

```python
import threading

# 创建全局ThreadLocal对象:
local_school = threading.local()

def process_student():
    # 获取当前线程关联的student:
    std = local_school.student
    print('Hello, %s (in %s)' % (std, threading.current_thread().name))

def process_thread(name):
    # 绑定ThreadLocal的student:
    local_school.student = name
    process_student()

t1 = threading.Thread(target= process_thread, args=('Alice',), name='Thread-A')
t2 = threading.Thread(target= process_thread, args=('Bob',), name='Thread-B')
t1.start()
t2.start()
t1.join()
t2.join()

### 运行结果
Hello, Alice (in Thread-A)
Hello, Bob (in Thread-B)
```

全局变量local_school就是一个ThreadLocal对象，每个Thread对它都可以读写student属性，但互不影响。你可以把local_school看成全局变量，但每个属性如local_school.student都是线程的局部变量，可以任意读写而互不干扰，也不用管理锁的问题，ThreadLocal内部会处理。

可以理解为全局变量local_school是一个dict，不但可以用local_school.student，还可以绑定其他变量，如local_school.teacher等等。

ThreadLocal最常用的地方就是为每个线程绑定一个数据库连接，HTTP请求，用户身份信息等，这样一个线程的所有调用到的处理函数都可以非常方便地访问这些资源。


## 例子

1、start()和join()的执行顺序

如果多线程中start()和join()的执行顺序是如下的：

```python
from threading import *

localinfo = local()

def printName():
	n = 10
	while n > 0:
		print("thread %s >>> %s >>> %d" % (current_thread().name, localinfo.name, n))
		n -= 1
		
def run(name):
	localinfo.name = name
	printName()

t1 = Thread(target=run, args=('boy1',))
t2 = Thread(target=run, args=('girl1',))

t1.start()
t2.start()
t1.join()
t2.join()

#####运行结果
thread Thread-1 >>> boy1 >>> 10
thread Thread-1 >>> boy1 >>> 9
thread Thread-2 >>> girl1 >>> 10
thread Thread-1 >>> boy1 >>> 8
thread Thread-2 >>> girl1 >>> 9
thread Thread-1 >>> boy1 >>> 7
thread Thread-2 >>> girl1 >>> 8
thread Thread-1 >>> boy1 >>> 6
thread Thread-2 >>> girl1 >>> 7
thread Thread-1 >>> boy1 >>> 5
thread Thread-2 >>> girl1 >>> 6
thread Thread-1 >>> boy1 >>> 4
thread Thread-2 >>> girl1 >>> 5
thread Thread-1 >>> boy1 >>> 3
thread Thread-2 >>> girl1 >>> 4
thread Thread-1 >>> boy1 >>> 2
thread Thread-2 >>> girl1 >>> 3
thread Thread-1 >>> boy1 >>> 1
thread Thread-2 >>> girl1 >>> 2
thread Thread-2 >>> girl1 >>> 1
```

由于在线程执行函数需要比较长的时间，操作系统会在t1和t2线程间不断切换，而join()的作用仅仅是使得在子线程还未结束时，主线程不能提前结束，所以此时的输出顺序会显得很乱。

如果改成串行，相当于阻塞，使得主线程当做两个线程中间的切换开关，start()和join()的顺序就应该如下：

```python
from threading import *

localinfo = local()

def printName():
	n = 10
	while n > 0:
		print("thread %s >>> %s >>> %d" % (current_thread().name, localinfo.name, n))
		n -= 1
		
def run(name):
	localinfo.name = name
	printName()

t1 = Thread(target=run, args=('boy1',))
t2 = Thread(target=run, args=('girl1',))

t1.start()
t1.join()

t2.start()
t2.join()

####运行结果
thread Thread-1 >>> boy1 >>> 10
thread Thread-1 >>> boy1 >>> 9
thread Thread-1 >>> boy1 >>> 8
thread Thread-1 >>> boy1 >>> 7
thread Thread-1 >>> boy1 >>> 6
thread Thread-1 >>> boy1 >>> 5
thread Thread-1 >>> boy1 >>> 4
thread Thread-1 >>> boy1 >>> 3
thread Thread-1 >>> boy1 >>> 2
thread Thread-1 >>> boy1 >>> 1
thread Thread-2 >>> girl1 >>> 10
thread Thread-2 >>> girl1 >>> 9
thread Thread-2 >>> girl1 >>> 8
thread Thread-2 >>> girl1 >>> 7
thread Thread-2 >>> girl1 >>> 6
thread Thread-2 >>> girl1 >>> 5
thread Thread-2 >>> girl1 >>> 4
thread Thread-2 >>> girl1 >>> 3
thread Thread-2 >>> girl1 >>> 2
thread Thread-2 >>> girl1 >>> 1
````

2、

```python
import threading

local_school=threading.local()

class myClass(object):
    def __init__(self,name,age):
        self.name=name
        self.age=age

def process_student():
    std=local_school.student
    print('hello %s in(%s)' %(std,threading.current_thread().name))

def process_student_class():
    myclass=local_school.myclass
    print('my name is %s age %d in(%s)' %(myclass.name,myclass.age,threading.current_thread().name))

def process_thread(name):
    local_school.student=name
    process_student()

def process_thread_class(my):
    local_school.myclass=my
    process_student_class()

t1=threading.Thread(target=process_thread,args=('Alice',),name='Thread-A')
t2=threading.Thread(target=process_thread,args=('Bob',),name='Thread-B')
cs3=myClass('Jack',23)
t3=threading.Thread(target=process_thread_class,args=(cs3,),name='Thread-C')
cs4=myClass('Tom',18)
t4=threading.Thread(target=process_thread_class,args=(cs4,),name='Thread-D')
t1.start()
t2.start()
t3.start()
t4.start()
t1.join()
t2.join()
t3.join()
t4.join()
print('All therad end.')

####运行结果
hello Alice in(Thread-A)
hello Bob in(Thread-B)
my name is Jack age 23 in(Thread-C)
my name is Tom age 18 in(Thread-D)
All therad end.
```


## 参考

[深入理解Python中的ThreadLocal变量（上）](http://python.jobbole.com/86150/ "深入理解Python中的ThreadLocal变量（上）")

[深入理解Python中的ThreadLocal变量（中）](http://python.jobbole.com/86203/ "深入理解Python中的ThreadLocal变量（中）")

[深入理解Python中的ThreadLocal变量（下）](http://python.jobbole.com/86753/ "深入理解Python中的ThreadLocal变量（下）")


## 链接

上一节 [Chapter_047 多线程](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_047.md "Chapter_047 多线程")

下一节 [Chapter_049 进程 vs 线程](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_049.md "Chapter_049 进程 vs 线程")
