---
layout:     post
title:      ipdb调试代码--Python Debug工具
date:       2020-11-22
author:     AspenStars
header-img: img/post-bg-map.jpg
catalog: true
tags:
    - Python
    - ipdb
    - debug
---

# IPDB介绍
[ipdb](https://pypi.org/project/ipdb/)具有与pdb模块相同的接口，通过调用IPython Debugger（调试器）实现Python代码的Debug。相比与IPython Debugger，IPDB增加了一些实用的特性，比如增加制表符补全，语法突出，更好的tracebacks，更好的introspection

# 安装方式

1. pip
```bash
pip install ipdb
```

2. conda 
```bash
conda install -c conda-forge ipdb
```

# 使用方式

## 集成到源代码中
通过在代码开头导入包，可以直接在代码指定位置插入断点。如下所示：
```python
import ipdb
# some code
x = 10
ipdb.set_trace()
y = 20
# other code
```
则程序会在执行完x = 10这条语句之后中断，展开Ipython环境，进行调试

## 命令式
上述方法需要在源代码中增加语句造成代码污染，更推荐使用命令式进行调试
```bash
python -m ipdb your_code.py
```
使用上述命令会在进入源代码的第一行中断，便可以开始使用命令进行调试了

# 常用的调试命令
ipdb 的命令跟 pdb 是一样的, 更多的用法可参考[pdb文档](https://docs.python.org/3.5/library/pdb.html)
## 断点设置
 | 命令 | 含义 | 
 | --- | --- | 
 | `b  10` | 断点设置在本文件的第10行 | 
 | `b  ots.py:20`  | 断点设置到 ots.py第20行, ots.py需要在 sys.path 中，当前目录已经默认存在 sys.path , 可通过 `..` 引用上一层目录的文件 | 
 | `condition bpnumber [condition]` | 只有当`[condition]`这个Bool表达式为 `true` 时, 才会设置这个断点 | 
 | `b`  | 查看断点及编号 | 
 | `cl 2`  | 删除第2个断点 | 
 | `disable` |  取消所有断点的功能,但仍然保留这些断点 | 
 | `enable` |  恢复断点的功能 | 
 | `tbreak` | 只生效一次的断点，使用方法同命令 `b` | 

## 运行
 | 命令 | 含义 | 
 | --- | --- | 
 | `n`(next) | 单步运行 | 
 | `s`(step)) | 细点运行, 即如果当前是一个函数，会进入函数内部 | 
 | `c`(continue) | 一直运行到下一个断点 | 
 | `<ENTER>` | 重复上次命令 | 
 | `j(ump) lineNumber` |  忽略某段代码,直接跳到某行执行。只有在 call stack 的最底部才能作用 | 
 | `a` |  查看全部栈内变量 | 
 | `r`(return) |  直接执行至 return 语句 | 
 | `l`(list) |  查看运行到某处代码，`l 2,5` 查看第 2-5 行的源码 | 
 | `ll` |  查看整个源文件 | 
 | `p param` |  查看当前 变量`param`的值 | 

## 重启或退出 debugger
`restart` 或 `run` 命令

需要注意的是，重启 debugger 后断点、debugger 的设置等是会保留的。

如果要一个全新的 debugger，可通过命令 `q`、`quit` 或 `exit` 退出 debugger 后进入

## 其他不太常用的命令
 | 命令 | 含义 | 
 | --- | --- | 
 | `w` |  列出目前call stack 中的所在层 | 
 | `d` |  在call stack中往下移一层 | 
 | `u` |  在call stack中往上移一层。如果在上移一层之后按下 n ,则会在上移之后的一层执行下一个叙述,之前的 function call 就自动返回 | 
 | `ignore` |  设定断点的忽略次数。如果没指定 count,其初始 为 0。当 count 为 0 时,断点会正常动作。若有指定 count,则每次执行到该中断, count 就少 1,直到 count 数为 0 | 
 | `pp` |  和 p 命令类似,但是使用 pprint module(没用过 pprint,详情请参考 Python Library Reference) | 
 | `alias` |  以一个"别名"代替"一群除错命令",有点类似 c/c 的 macro(详情请参考 Python Library Reference) | 
 | `unalias` |  取消某个 alias | 
 | `[!]statement` |  在目前的环境(context)中执行叙述 | 

## 参考文献
1. [ipdb 使用小记](http://wulc.me/2018/12/21/ipdb%20%E4%BD%BF%E7%94%A8%E5%B0%8F%E8%AE%B0/)
2. [使用IPDB调试Python代码](https://xmfbit.github.io/2017/08/21/debugging-with-ipdb/)
3. [The python debugger(PDB)的简介](https://www.cnblogs.com/wei-li/archive/2012/05/02/2479082.html)