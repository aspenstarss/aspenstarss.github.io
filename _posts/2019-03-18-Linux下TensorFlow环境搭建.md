---
layout:     post
title:      Linux下非root用户TensorFlow GPU环境搭建
subtitle:   搭环境总是一把心酸一把泪
date:       2019-03-18
author:     AspenStars
header-img: img/post-bg-map.jpg
catalog: true
tags:
    - Linux
    - TensorFlow
    - CUDA
    - CUDNN
---

更新于2020-4-21

## 安装前的准备工作

#### TensorFlow与CUDA、cuDNN版本的对应关系

cuda与显卡驱动的对应关系见[NVIDIA CUDA Toolkit Release Notes](https://docs.nvidia.com/cuda/cuda-toolkit-release-notes/index.html)中CUDA Driver项

 | 版本 | Python 版本 | 编译器 | 构建工具 | cuDNN | CUDA |
 | :---: | :---: | :---: | :---: | :---: | :---: |
tensorflow_gpu-1.12.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.15.0 | 7 | 9
tensorflow_gpu-1.11.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.15.0 | 7 | 9
tensorflow_gpu-1.10.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.15.0 | 7 | 9
tensorflow_gpu-1.9.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.11.0 | 7 | 9
tensorflow_gpu-1.8.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.10.0 | 7 | 9
tensorflow_gpu-1.7.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.9.0 | 7 | 9
tensorflow_gpu-1.6.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.9.0 | 7 | 9
tensorflow_gpu-1.5.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.8.0 | 7 | 9
tensorflow_gpu-1.4.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.5.4 | 6 | 8
tensorflow_gpu-1.3.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.4.5 | 6 | 8
tensorflow_gpu-1.2.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.4.5 | 5.1 | 8
tensorflow_gpu-1.1.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.4.2 | 5.1 | 8
tensorflow_gpu-1.0.0 | 2.7、3.3-3.6 | GCC 4.8 | Bazel 0.4.2 | 5.1 | 8

数据来源：[TensorFlow](https://www.tensorflow.org/install/source#linux)

根据TensorFlow[官方回复](https://github.com/tensorflow/tensorflow/issues/26182#issuecomment-467986463) 目前已知tensorflow-gpu-1.13以上版本是基于CUDA10、cuDNN7构建

#### 顺序

1. 根据上表自己需要的Tensorflow版本下载对应版本的CUDA和cuDNN
2. 安装CUDA(在此之前还需要安装好NVIDIA显卡驱动，本篇不详述)
3. 解压cuDNN(这个只文件是个压缩包),并根据教程操作
4. 测试是否安装成功
5. 安装Anaconda并创建所需环境
6. 安装tensorflow-gpu

#### 下载地址

[CUDA](https://developer.nvidia.com/cuda-toolkit-archive)

[cudnn(需要注册账号)](https://developer.nvidia.com/cudnn)  
cuDNN（CUDA Deep Neural Network library）：是NVIDIA打造的针对深度神经网络的加速库，是一个用于深层神经网络的GPU加速库。如果你要用GPU训练模型，cuDNN不是必须的，但是一般会采用这个加速库。

[Anaconda](https://repo.anaconda.com/archive/)

## 安装CUDA

1. 增加CUDA文件的执行权限  
`chmod +x 你下载的cuda文件`
2. 运行cuda文件(在cuda文件所在目录执行)  
`./你下载的cuda文件`
***
2020-04-21更新

详见：[参考资料1](https://forums.developer.nvidia.com/t/failing-to-install-10-1-via-run-file-on-rhel7-as-non-root/72087/5)

这一次我装了cuda10.1版本，安装方式有所改变  

`cuda_10.1.105_418.39_linux.run`是你下载的文件名，根据自己的情况确定

`$HOME/cuda10.1`是将cuda安装在你的`home`目录下的`cuda10.1`文件夹中
```
./cuda_10.1.105_418.39_linux.run --toolkit --toolkitpath=$HOME/cuda10.1 --defaultroot=$HOME/cuda10.1
```
进入后将安装显卡驱动(driver)选项去掉，按Enter键  

最后切换到`install`安装

***

3. 按`Ctrl+F`迅速阅读完安装协议
4. 输入`accept`同意安装协议
5. 询问是否安装显卡驱动(driver installation)，建议在此之前安装，此处选择**不安装（no）** 
6. 输入cuda安装的位置
    - 如果是root用户，可以直接使用默认选项（请记住这个目录，之后要用）
    - 非root用户，输入个人用户的目录，如`/home/yourname/cuda9`,
7. cuda samples目录同上

## 安装cuDNN

1. 解压cuDNN文件得到CUDA文件夹  
`tar -xzvf cudnn-9.0-linux-x64-v7.tgz`（后面的名称根据自己的版本确定）
2. 将cuDNN拷贝到CUDA文件夹  
(cuda9是上一项第6步时的目录，如装在个人用户的下的话就是`/home/yourname/cuda9`,使用`sudo`的话应该是在`/usr/local/cuda9.0`下面)  
`cp cuda/include/cudnn.h cuda9/include/`  
`cp cuda /lib64/libcudnn* cuda9/lib64 `
3. 为各文件增加可读权限（有的本身就有可读权限，此处建议执行）
`chmod a+r cuda9/include/cudnn.h cuda9/lib64/libcudnn* `

## 修改环境变量，使系统能够识别新的安装

环境变量文件位于 `~/.bashrc`(用`vi ~/.bashrc`编辑)   
将光标移到最后一行，按`i`进入编辑模式，然后输入  
```
export PATH=$HOME/cuda9/bin:$PATH
export LD_LIBRARY_PATH=$HOME/cuda9/lib64/:$LD_LIBRARY_PATH
```

修改之后运行`source ~/.bashrc`使环境变量生效

## 测试CUDA和cuDNN

1. 进入安装CUDA时输入的cuda samples目录
`cd NVIDIA_CUDA-9.0_Samples` 或者 `cd cuda/samples/1_Utilities/deviceQuery`  
2. 输入`make`编译（如果之前执行过`make`，请先执行`make clean`清理）
***
如果此处出现`nvcc fatal   : Unsupported gpu architecture 'compute_75'`错误

在Makefile的275行附近，有一个`SMS ?= 30 35 37 50 52 60 61 70 75`

删除大于你GPU算力的数，比如我的是`GTX 1080Ti`，算力是6.1，所以删去70和75

即改成`SMS ?= 30 35 37 50 52 60 61`

重新make即可

[算力查询](https://developer.nvidia.com/cuda-gpus#compute),进去选择你GPU的系列型号即可看到
***
3. 进入设备信息查询目录  
`cd ~/NVIDIA_CUDA-9.0_Samples/bin/x86_64/linux/release`
4. 运行` ./deviceQuery `  
如果出现显卡信息，且最后一行为`Result = PASS`即为成功
5. [可选的]检查一下系统和CUDA-Capable device的连接情况  
`./bandwidthTest`

#### 查看CUDA、cuDNN版本

CUDA：`cat cuda所在目录/version.txt`  
cuDNN：`cat cuda所在目录/include/cudnn.h | grep CUDNN_MAJOR -A 2`

cuda所在目录：
- 如装在个人用户的下的话就是`/home/yourname/cuda9`
- 使用`sudo`的话应该是在`/usr/local/cuda9.0`

## 安装Anaconda

1. 增加Anaconda文件的执行权限  
`chmod +x 你下载的Anaconda文件`
2. 运行Anaconda文件(在Anaconda文件所在目录执行，不建议使用sudo)  
`./你下载的Anaconda文件`
3. 按`Ctrl+F`迅速阅读完安装协议
4. 输入`accept`同意安装协议
5. 在让你添加PATH前，一路回车默认安装（不要一下就按完了）
6. 当出现让你添加PATH的时候（以下文字） ，输入`yes` 
```
Do you wish the installer to prepend the Anaconda3 install location
to PATH in your /home/hj/.bashrc ? [yes|no]
```
7. 如果上一步按了默认，则手动在.bashrc文件中添加以下内容  
`export PATH=$HOME/anaconda3/bin:$PATH`（如果安装在其他目录请将$HOME更换成对应目录）  
然后  
`source ~/.bashrc`
8. 创建新的虚拟环境  
`conda create -n 环境名称`
9. 启动虚拟环境  
`source activate 环境名称`
10. 安装tensorflow-gpu  
`pip install tensorflow==1.12`(1.12可替换为你的指定版本)

**至此安装完毕**

参考资料
1. [NVIDIA Corporation - Failing to install 10.1 via .run file on RHEL7 as non-root](https://forums.developer.nvidia.com/t/failing-to-install-10-1-via-run-file-on-rhel7-as-non-root/72087/5)