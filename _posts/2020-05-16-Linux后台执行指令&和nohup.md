---
layout:     post
title:      Linux后台执行指令&和nohup
subtitle:   
date:       2020-05-16
author:     AspenStars
header-img: img/post-bg-keybord.jpg
catalog: true
tags:
    - Linux
    - Terminal
    - Command
---
# Linux后台执行指令:&和nohup

## &
在命令后面加上`&` 实现后台运行

实例：
```bash
python test.py &

jupyter notebook &
```

适合一些执行时间较长的非交互式命令，比如开启`jupater notebook`，查找`find`等等

如果需要输出信息，可以将输出信息重定向到文本文件

```bash
jupyter notebook > out.txt 2 > &1 &
```
这条命令将所有的标准输出和错误输出都重定向到一个叫做`out.txt`的文件中

**终止进程**

当成功地提交进程以后，会显示一个进程号，可以用它来监控该进程或杀死它，也可以使用下面的命令找到进程号`pid`，然后使用`kill`杀掉
```bash
ps -ef | grep jupyter
kill -9 进程号
```

## nohup
使用&命令后，作业被提交到后台运行，当前控制台没有被占用，但是一但把当前控制台关掉(退出帐户时)，作业就会停止运行。

nohup命令可以在你退出帐户之后继续运行相应的进程。

nohup就是不挂起的意思( no hang up)。

该命令的一般形式为：
```bash
nohup command &
```

如果使用nohup命令提交作业，那么在默认情况下该作业的所有输出都被重定向到一个名为nohup.out的文件中，除非另外指定了输出文件：
```bash
nohup command > myout.file 2>&1 &
```

## 其他
将一个正在前台执行的命令放到后台，并且处于暂停状态:
```
ctrl + z
```

终止前台命令:
```
Ctrl+c
```

查看当前有多少在后台运行的命令
```
jobs
```

`jobs -l`选项可显示所有任务的PID

jobs的状态可以是
- running
- stopped
- Terminated

但是如果任务被终止了（kill），shell 从当前的shell环境已知的列表中删除任务的进程标识

### 2>&1解析
 ```
 command >out.file 2>&1 &
 ```
 Linux中标准输出是1，标准错误输出是2  

 因此`2>&1`是将标准错误输出重定向到标准输出
 
 标准输出重定向到`out.file`中
 
 因此就是将标准输出和错误输出都重定向到这个文件中

 ### 参考资料
 1. [liuyanfeier - linux后台执行命令：&和nohup](https://blog.csdn.net/liuyanfeier/article/details/62422742)
 
 写得非常清楚，本文大部分均参考此文章，在实践的基础上进行了修改