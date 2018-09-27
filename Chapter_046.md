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
