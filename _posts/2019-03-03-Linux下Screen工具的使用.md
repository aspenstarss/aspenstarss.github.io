---
layout:     post
title:      Linux下Screen工具的使用
subtitle:   Screen工具使用的总结
date:       2019-03-03
author:     AspenStars
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Screen
    - Linux
---

## Screen工具是什么

Screen 是一款由GNU计划开发的用于命令行终端切换的自由软件。

用户可以通过该软件同时连接多个本地或远程的命令行会话，并在其间自由切换。

很重要的一点：Screen能让你**关闭Terminal后，服务器上的程序还能继续执行**,即*会话恢复*

另外还提供**多窗口**的功能，只需要与服务器建立一个连接即可打开多个窗口执行任务

## 命令格式

```bash
screen [-opts] [cmd [args]]
screen -r [host.tty]
```

### 选项(即上述命令中`-opts`中可以输入的内容)

```bash
-A 　将所有的视窗都调整为目前终端机的大小。
-d <作业名称> 　将指定的screen作业离线。
-h <行数> 　指定视窗的缓冲区行数。
-m 　即使目前已在作业中的screen作业，仍强制建立新的screen作业。
-r <作业名称> 　恢复离线的screen作业。
-R 　先试图恢复离线的作业。若找不到离线的作业，即建立新的screen作业。
-s 　指定建立新视窗时，所要执行的shell。
-S <作业名称> 　指定screen作业的名称。
-v 　显示版本信息。
-x 　恢复之前离线的screen作业。
-ls或--list 　显示目前所有的screen作业。
-wipe 　检查目前所有的screen作业，并删除已经无法使用的screen作业。
```

### 常用的Screen命令

```bash
screen -S yourname -> 新建一个叫yourname的session
screen -ls -> 列出当前所有的session
screen -r yourname -> 回到yourname这个session
screen -d yourname -> 远程detach某个session
screen -d -r yourname -> 结束当前session并回到yourname这个session
```

### 启动Screen窗口后常用的指令

因为screen实际上是一个程序，所有不能直接在里面使用Terminal的一些命令，需要一个前置标志来告诉Screen你要发送指令了

在Screen中这个前置标志就是`Ctrl + a`（下面用`C-a`代替）,注意，这里是两个键一起按（先`Ctrl`），前置指令按了之后，即可松开，只按剩下的命令即可

```bash
C-a ? -> 显示所有键绑定信息
C-a c -> 创建一个新的运行shell的窗口并切换到该窗口
C-a n -> Next，切换到下一个 window 
C-a p -> Previous，切换到前一个 window 
C-a 0..9 -> 切换到第 0..9 个 window
Ctrl+a [Space] -> 由视窗0循序切换到视窗9
C-a C-a -> 在两个最近使用的 window 间切换 
C-a x -> 锁住当前的 window，需用用户密码解锁
C-a d -> detach，暂时离开当前session，将目前的 screen session (可能含有多个 windows) 丢到后台执行，并会回到还没进 screen 时的状态，此时在 screen session 里，每个 window 内运行的 process (无论是前台/后台)都在继续执行，即使 logout 也不影响。 
C-a z -> 把当前session放到后台执行，用 shell 的 fg 命令则可回去。
C-a w -> 显示所有窗口列表
C-a t -> time，显示当前时间，和系统的 load 
C-a k -> kill window，强行关闭当前的 window
C-a [ -> 进入 copy mode，在 copy mode 下可以回滚、搜索、复制就像用使用 vi 一样
            C-b Backward，PageUp 
            C-f Forward，PageDown 
            H(大写) High，将光标移至左上角 
            L Low，将光标移至左下角 
            0 移到行首 
            $ 行末 
            w forward one word，以字为单位往前移 
            b backward one word，以字为单位往后移 
            Space 第一次按为标记区起点，第二次按为终点 
            Esc 结束 copy mode 
C-a ] -> paste，把刚刚在 copy mode 选定的内容贴上
```

## 使用示例

#### 创建一个新的窗口

`screen -S` + **你窗口的名字**

比如`screen -S hello`

此处可以直接使用命令`screen`就可以启动。但是这样启动的screen会话没有名字，实践上推荐为每个screen会话取一个名字，方便分辨。

screen启动后，会创建第一个窗口，也就是窗口No. 0，并在其中打开一个系统默认的shell，一般都会是bash。所以你敲入命令screen之后，会立刻又返回到命令提示符，仿佛什么也没有发生似的，其实你已经进入Screen的世界了。

#### 会话分离与恢复

你可以使用Screen命令 `C-a d` 不中断screen窗口中程序的运行而暂时断开（detach）screen会话，并在随后使用 `screen -r hello` 重新连接（attach）该会话，重新控制各窗口中运行的程序。

#### 关闭窗口

正常情况下，当你退出一个窗口中最后一个程序（通常是bash）后，这个窗口就关闭了。

另一个关闭窗口的方法是使用`C-a k`杀死当前的窗口，同时也将杀死这个窗口中正在运行的进程。

还可以使用快捷键`C-a quit`命令退出Screen会话

#### 清除会话

如果由于某种原因其中一个会话死掉了（例如人为杀掉该会话），这时screen -list会显示该会话为dead状态。使用`screen -wipe`命令清除该会话

如果出现你已经退出Screen窗口，但是使用`screen -ls`显示会话仍为**Attached**状态，使你无法再次连接到这个会话的话，可以使用`screen -D ＜session-id>`强制关闭连接，然后就能再次连接了

#### 查看窗口和窗口名称

打开多个窗口后，可以使用快捷键C-a w列出当前所有窗口。如果使用文本终端，这个列表会列在屏幕左下角，如果使用X环境下的终端模拟器，这个列表会列在标题栏里。窗口列表的样子一般是这样：

`0$ bash  1-$ bash  2*$ bash`
这个例子中表示开启了三个窗口，其中*号表示当前位于窗口2，-号表示上一次切换窗口时位于窗口1。

可以使用快捷键C-a A来为当前窗口重命名，按下快捷键后，Screen会允许你为当前窗口输入新的名字，回车确认。

#### 参考链接

1、[Linux screen 命令详解：用于命令行终端切换](https://wangchujiang.com/linux-command/c/screen.html)

2、[解决screen状态为Attached连上不的问题](https://www.centos.bz/2011/07/screen-attached-issue/)


