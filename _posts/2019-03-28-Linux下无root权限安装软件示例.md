---
layout:     post
title:      Linux下无root权限安装软件示例
subtitle:   遇见即更新，方便查看，熟悉命令
date:       2019-03-28
author:     AspenStars
header-img: img/post-bg-keybord.jpg
catalog: true
tags:
    - Linux
    - root
    - 安装软件
---

在没有root权限的时候，无法使用apt等包管理软件安装程序，此时可以通过下载源码，自行编译，加入PATH路径实现软件的安装，这样也只会影响自己的环境，不会对整个服务器上其他人产生影响。

我们以tree1.6.0作为示例,可以延伸拓展到所有其他软件

## 步骤概览
1. 下载软件源码
2. 解压
3. 编译
4. 建立bin目录
5. 建立软连接
6. 添加到PATH
7. 生效PATH
8. 尽情享受软件

## 安装过程
1. 下载软件源码
这里可以先进入你的下载目录，比如Download目录，以免找不到下载的文件

这里提供一份tree的源码下载地址，来自[Indiana State University](https://www.indstate.edu/)，其他软件可以尝试在[GitHub](https://github.com/)或者Google寻找。
```
wget http://mama.indstate.edu/users/ice/tree/src/tree-1.6.0.tgz
```

2. 解压
```
tar -zxvf <你下载的文件名> # gz压缩格式
tar -jxvf <你下载的文件名> # bz2压缩格式
```
例如
```
tar -zxvf tree-1.6.0.tgz
```

3. 进入解压后的文件夹，并编译
```
cd tree-1.6.0
make
```

4. 建立bin目录
为了不把PATH路径弄得太乱，可以在自己目录下建立一个`local/bin`目录，这次添加路径到PATH后，以后也不用再添加了
```
cd ~
mkdir local
mkdir local/bin
```

5. 建立软连接
将刚刚编译得到的文件软连接(快捷方式)到刚刚生成的bin目录
```
ln -s <源文件> <目标文件>
```
此处建议使用绝对目录  
**示例**  
编译生成的文件：`/home/myname/tree-1.6.0/tree`  
```
ln -s /home/myname/tree-1.6.0/tree /home/myname/local/bin/tree
```

6. 添加到PATH
添加到自己的PATH中，才能直接使用命令
```
vi ~/.bashrc
```
使用`Ctrl + F`移动到文件末尾，按`i`,在现有内容最后一行后新建一行，输入
```
export PATH=/home/myname/local/bin:$PATH
```
按`ESC`输入`：`，在输入`wq`保存并退出

7. 生效PATH
```
source ~/.bashrc
```

8. 尽情享受软件