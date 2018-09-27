# Chapter_045 进程和线程

## 进程

对于操作系统来说，一个任务就是一个进程（Process）。

## 线程

一个进程内部，要同时做多件事，就需要同时运行多个“子任务”，每一个“子任务”就是一个线程（Thread）。

> *一个进程至少有一个线程*

## 多任务

实现多任务有如下三种方式：

1. 多进程模式（每一个进程包含一个线程）

2. 多线程模式（只有一个进程，这个进程包含多个线程）

3. 多进程+多线程模式（多个进程，每一个进程又包含多个线程）


## 链接

上一节 [Chapter_044 序列化](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_044.md "Chapter_044 序列化")

下一节 [Chapter_046 多进程](https://github.com/nizo2010/Study_Python_lxf/blob/master/Chapter_046.md "Chapter_046 多进程")
