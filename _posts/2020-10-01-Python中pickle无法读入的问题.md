---
layout:     post
title:      pickle读入时提示AttributeError: Can't get attribute 'XXX' on <module '__main__' from '
date:       2020-10-01
author:     AspenStars
header-img: img/post-bg-map.jpg
catalog: true
tags:
    - Python
    - pickle
    - 解决错误
---

[解决方案来自StackoverFlow](https://stackoverflow.com/questions/50465106/attributeerror-when-reading-a-pickle-file)

## 问题描述

当使用pickle读入数据时`data = pickle.load(f)`，提示`AttributeError: Can't get attribute 'XXXX' on <module '__main__' from ' XXX`错误

## 问题分析

名称空间不同造成的错误

当直接运行`python`文件时，将会以`__main__`为module名运行程序（无论其实际文件名如何）。 但是，当使用`import`导入时，则其module名取决于文件名（例如`import program`时，其中引入的`class`和`function`将以`program`为module名）

`pickle`很懒，不会序列化类定义或函数定义，只保存有关如何查找类的reference（类所在的模块及其名称）

所以有可能是`dump`的时候以`__main__`为名称空间，`load`的时候又以具体文件名称为名称空间，所以找不到

因此在使用`pickle`存储数据的时候应该**避免**把要pickle的类和函数声明在`main`模块中

## 解决方案

三种解决方案

1. **最简单便捷的方案**，不要pickle在`__main__`模块中的类
可以单独写一个文件来存这个`class`，然后用`import`导入

2. 救急的方案，针对已经存在的，无法重新pickle的文件，写一个**自定义反序列化**
```python
import pickle

class MyCustomUnpickler(pickle.Unpickler):
    def find_class(self, module, name):
        if module == "__main__":
            module = "<填写那个class对应的module名，即导入的文件名>"
        return super().find_class(module, name)

with open('out.pkl', 'rb') as f:
    unpickler = MyCustomUnpickler(f)
    obj = unpickler.load()

print(obj)
print(obj.name)
```

这是加载已经创建的`pickle`文件的最简单的方法。这个程序把责任推给反序列化代码，而实际上应该由序列化代码来正确地创建`pickle`文件

3. **不推荐**，自定义一个序列化方案

与前面的解决方案相比，这个方案可以确保序列化的pickle对象可以被任何人轻松地反序列化，而不需要知道定制的反序列化逻辑。

要做到这一点，可以使用copyreg模块来通知pickle如何反序列化各种类。

所以在这里要做的是告诉pickle反序列化所有main类的实例，需要为每个类注册一个自定义序列化器
```python
import program
import pickle
import copyreg

class MyClass:
    def __init__(self, name):
        self.name = name

def pickle_MyClass(obj):
    assert type(obj) is MyClass
    return program.MyClass, (obj.name,)

copyreg.pickle(MyClass, pickle_MyClass)

if __name__ == '__main__':
    o = MyClass('test')
    with open('out.pkl', 'wb') as f:
        pickle.dump(o, f)
```