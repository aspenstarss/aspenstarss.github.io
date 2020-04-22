---
layout:     post
title:      远程使用Jupyter Notebook配置
subtitle:   2020新的开始
date:       2020-03-14
author:     AspenStars
header-img: img/post-bg-keybord.jpg
catalog: true
tags:
    - Python
    - Jupyter Notebook
    - 服务器
---
## ssh 配置

1. SSH连接服务器

`ssh username@ip`  
- `username`是服务器上的用户名  
- `ip`是服务器ip

    可选参数

    - `-p 端口号` 连接服务器指定端口
    - `-i 密钥路径` 使用指定路径的密钥连接服务器，避免输入密码（不输入时会默认去`~/.ssh/id_rsa`寻找密钥，没有或者密钥未授权才会让输入密码）

2. SSH configrc文件配置

使用SSH的config文件可以免去每次输入`username`和`ip`的麻烦，直接使用主机名即可访问

默认是没有ssh的config文件的，输入`vim ~/.ssh/config`生成并配置

简单的config文件格式为

```bash
host 你设置的服务器名
    hostname 服务器的ip
    username 用户名
    port 默认为22端口，如果你服务器改了端口，在此设置
```

3. 添加公钥到服务器授权列表

服务器端.ssh目录中新建一个`authorized_keys`的文件

将公钥内容拷贝到这个文件中，设置权限600
```bash
vim authorized_keys //创建authorized_keys文件，打开后将本地的公钥内容复制到这个文件中

chmod  -R 600 authorzied_keys //修改权限，禁止除自己和root外的所有其他用户查看、修改
```

## 通过ssh建立通道

1. 在远程服务器上，启动jupyter notebook服务：
```
jupyter notebook --no-browser --port=8889
```

2. 在本地终端中启动SSH：
```
ssh -N -f -L localhost:8888:localhost:8889 username@serverIP
```
其中： 
- `-N` 告诉SSH没有命令要被远程执行； 
- `-f` 告诉SSH在后台执行； 
- `-L` 是指定port forwarding的配置，远端端口是8889，本地的端口号的8888

**注意**：`username@serverIP`替换成服务器的对应用户名和IP地址，也可使用第一步设置的服务器别名

3. 打开浏览器，访问：`http://localhost:8888/`

## 上传本地文件到服务器

- 实例1：从远处复制文件到本地目录
```
$scp root@10.6.159.147:/opt/soft/demo.tar /opt/soft/
```
说明： 从`10.6.159.147`机器上的`/opt/soft/`的目录中下载`demo.tar` 文件到本地`/opt/soft/`目录中

- 实例2：从远处复制到本地
```
$scp -r root@10.6.159.147:/opt/soft/test /opt/soft/
```
说明： 从`10.6.159.147`机器上的`/opt/soft/`中下载`test`目录到本地的`/opt/soft/`目录来。

- 实例3：上传本地文件到远程机器指定目录
```
$scp /opt/soft/demo.tar root@10.6.159.147:/opt/soft/scptest
```
说明： 复制本地`/opt/soft/`目录下的文件`demo.tar` 到远程机器`10.6.159.147`的`opt/soft/scptest`目录

- 实例4：上传本地目录到远程机器指定目录
```
$scp -r /opt/soft/test root@10.6.159.147:/opt/soft/scptest
```
说明： 上传本地目录 `/opt/soft/test`到远程机器`10.6.159.147`上`/opt/soft/scptest`的目录中

参考文献
1、[远程访问服务器Jupyter Notebook的两种方法](https://www.jianshu.com/p/8fc3cd032d3c)
2、[scp 跨机远程拷贝](https://linuxtools-rst.readthedocs.io/zh_CN/latest/tool/scp.html)