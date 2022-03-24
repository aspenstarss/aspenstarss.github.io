---
layout:     post
title:      sudoers改坏后无法使用sudo的解决办法
subtitle:   
date:       2022-03-24
author:     AspenStars
header-img: img/post-bg-universe.jpg
catalog: true
tags:
    - Linux
---

老师在服务器添加sudo权限的时候修改了`/etc/sudoers`的读写权限，由于组里服务器没有配置`root`账号，导致所有账户都出现了`sudo`权限全部失效的问题。但是这个文件必须由`sudo`进行操作，因此陷入僵局。

系统提示：
> /etc/sudoers is world writable

查阅网上的参考资料后，提示这条命令可以解决
```
pkexec chmod 440 /etc/sudoers
```

兴高采烈地拿到管理员密码去执行这条命令，但是又出现了下面的问题

>==== AUTHENTICATING FOR org.freedesktop.policykit.exec 
> ===Authentication is needed to run `/usr/sbin/visudo' as the super user
>
> Authenticating as: ubuntu,,, (ubuntu)
>
> Password:
> 
> polkit-agent-helper-1: error response to PolicyKit daemon: GDBus.Error:org.freedesktop.PolicyKit1.Error.Failed: No session for cookie==== AUTHENTICATION FAILED ===Error executing command as another user: Not authorized
> 
> This incident has been reported.

应该是没有`root`账号，只有`sudo`账号导致所有`sudo`账号权限失效的原因，这就陷入死循环中了

然后又查了大量资料，基本上都说要去物理机上启动另一个系统来修改权限

但是突然看到[这篇文章](https://kknews.cc/code/3nk85e8.html)成功解决了问题

## 解决方案如下

- 远程的话开两个ssh终端，能直接操作物理机的话使用ctrl+alt+F2 创建第二个终端窗口使用ctrl+alt+F1切换到tty1;ctrl+alt+F2切换到tty2
- 两个终端，都用有`sudo`权限的用户登录
- 对tty1终端：输入 `echo $$` // 获取pid
- 切换到tty2：输入`pkttyagent --process $上一步获取的pid值`，此时该tty2终端会卡住
- 切到tty1：输入 `pkexec visudo`，此时tty1也会卡住
- 切到tty2：会看到要求输入密码，对应输入
- 切回到tty1：发现已经进入了visudo编辑界面，修改出错的sudoers，保存（此处可能会提示保存到`sudoer.tmp`等文件，这里删除后缀直接保存）
- 修改完成，发现就可以继续使用sudo命令了

## 提示
`/etc/sudoers`文件应该使用专门的命令`visudo`进行修改，输入这个命令后会自动给出一个文本编辑器`nano`进行编辑。


另外：`nano`编辑器中
- `Ctrl-`键被表示为一个脱字符`(^)`，因此`Ctrl+W`被写成了`^W`，等等。
- `Alt-`键被表示为一个`M`（从"Meta"而来），因此`Alt+W`被写成了`M-W`。
