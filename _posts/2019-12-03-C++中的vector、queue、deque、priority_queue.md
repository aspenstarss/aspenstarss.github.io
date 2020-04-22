---
layout:     post
title:      C++中的vector、queue、deque、priority_queue
subtitle:   声明方式、常用函数、复杂度
date:       2019-12-03
author:     AspenStars
header-img: img/post-bg-keybord.jpg
catalog: true
tags:
    - 数据结构
    - 算法
    - C++
    - leetcode
---

### vector
位于头文件`<vector>`中

#### 时间复杂度
std::vector 是 STL 提供的 内存连续的 、 可变长度 的数组（亦称列表）数据结构。能够提供线性复杂度的插入和删除，以及常数复杂度的随机访问。
- 创建空vector：O(1)
- 访问：O(1)
- 插入push_back()：均摊复杂度为 常数 ，最坏为线性复杂度
- 删除末尾元素：O(1)

#### 构造函数
```
vector():创建一个空vector
vector(int nSize):创建一个vector,元素个数为nSize
vector(int nSize,const t& t):创建一个vector，元素个数为nSize,且值均为t
vector(const vector&):复制构造函数
vector(begin,end):复制[begin,end)区间内另一个数组的元素到vector中
```
#### 常用函数
```
void push_back(const T& x):向量尾部增加一个元素X
void pop_back():删除向量中最后一个元素
void clear():清空向量中所有元素
iterator begin():返回向量头指针，指向第一个元素
iterator end():返回向量尾指针，指向向量最后一个元素的下一个位置
bool empty() const:判断向量是否为空，若为空，则向量中无元素
int size() const:返回向量中元素的个数
int capacity() const:返回当前向量所能容纳的最大元素值
```

#### 实例
```c++

#include <string.h>  
#include <vector>  
#include <iostream>  
#include <algorithm>  //sort()  
using namespace std;

static bool cmp(const int &a,const int &b)
{
    return a < b; //升序排列（与符号方向相同），如果改为return a > b，则为降序
}

int main()
{
    vector<int> obj;  // 创建一个向量存储容器 int
    vector<int> data {1, 2, 3};//C++11支持列表初始化 

    obj.push_back(i);// push_back(elem)在数组最后添加数据 
    obj.pop_back();//去掉数组最后一个数据 
    obj.size()  //size()容器中实际数据个数 
    
    sort(obj.begin(),obj.end());//从小到大排序
    reverse(obj.begin(),obj.end());//从大到小排序
    sort(obj.begin(),obj.end(),cmp);//自定义排序函数

    cout<<obj[i]<<" ";  // 直接利用下标访问

    //使用迭代器将容器中数据输出 
    vector<int>::iterator it;//声明一个迭代器，来访问vector容器，作用：遍历或者指向vector容器的元素 
    for(it=obj.begin();it!=obj.end();it++)
    {
        cout<<*it<<" ";
    }

    //定义二维动态数组5行6列 
    int N=5, M=6; 
    vector<vector<int> > obj(N, vector<int>(M)); 
 
    return 0;
}
```

#### 其他函数
1. 其他函数
```
void swap(vector&):交换两个同类型向量的数据
void assign(int n,const T& x):设置向量中第n个元素的值为x
void assign(const_iterator first,const_iterator last):向量中[first,last)中元素设置成当前向量元素
```

2. 增加函数
```
void push_back(const T& x):向量尾部增加一个元素X
iterator insert(iterator it,const T& x):向量中迭代器指向元素前增加一个元素x
iterator insert(iterator it,int n,const T& x):向量中迭代器指向元素前增加n个相同的元素x
iterator insert(iterator it,const_iterator first,const_iterator last):向量中迭代器指向元素前插入另一个相同类型向量的[first,last)间的数据
```
3. 删除函数
```
iterator erase(iterator it):删除向量中迭代器指向元素
iterator erase(iterator first,iterator last):删除向量中[first,last)中元素
void pop_back():删除向量中最后一个元素
void clear():清空向量中所有元素
```
4. 遍历函数
```
reference at(int pos):返回pos位置元素的引用
reference front():返回首元素的引用
reference back():返回尾元素的引用
iterator begin():返回向量头指针，指向第一个元素
iterator end():返回向量尾指针，指向向量最后一个元素的下一个位置
reverse_iterator rbegin():反向迭代器，指向最后一个元素
reverse_iterator rend():反向迭代器，指向第一个元素之前的位置
```
5. 判断函数
```
bool empty() const:判断向量是否为空，若为空，则向量中无元素
```
6. 大小函数
```
int size() const:返回向量中元素的个数
int capacity() const:返回当前向量张红所能容纳的最大元素值
int max_size() const:返回最大可允许的vector元素数量值
```
7. 其他函数
```
void swap(vector&):交换两个同类型向量的数据
void assign(int n,const T& x):设置向量中第n个元素的值为x
void assign(const_iterator first,const_iterator last):向量中[first,last)中元素设置成当前向量元素
```

### queue
定义于头文件`<queue>`  
`queue`在底层容器尾端推入元素，从首端弹出元素。  
std::deque 是 STL 提供的 双端队列 数据结构。能够提供线性复杂度的插入和删除，以及常数复杂度的随机访问

#### 时间复杂度
- 访问：常数复杂度
- 创建空：常数复杂度
- 插入`push_front()`、 `push_back()`：常数复杂度
- 删除`pop_front()`、`pop_back()`：常数复杂度

#### 基本操作
```
push(x) 将x压入队列的末端
pop() 弹出队列的第一个元素(队顶元素)，注意此函数并不返回任何值
front() 返回第一个元素(队顶元素)
back() 返回最后被压入的元素(队尾元素)
empty() 当队列为空时，返回true
size() 返回队列的长度
```

#### 实例
1. 普通声明
```c++
queue<int> q;
```
2. 结构体
```c++
struct node
{
    int x,y;
};
queue<node>   q;
```
### deque
头文件：`<deuqe>`

deque容器类与vector类似，支持随机访问和快速插入删除，它在容器中某一位置上的操作所花费的是线性时间。  
与vector不同的是，deque还支持从开始端插入数据：push_front()

#### 时间复杂度
- 创建
- 插入
- 删除

#### 构造函数
```
deque<Elem> c; 创建一个空的deque
deque<Elem> c1(c2) 复制一个deque。
deque<Elem> c(n) 创建一个deque，含有n个数据，数据均已缺省构造产生。
deque<Elem> c(n, elem) 创建一个deque,元素个数为n,值为elem。
deque<Elem> c(beg,end) 创建一个以[beg;end)区间的deque。
~deque<Elem>() 销毁所有数据，释放内存。
```

#### 常用函数
```
void push_front(const T& x):双端队列头部增加一个元素X
void push_back(const T& x):双端队列尾部增加一个元素x
void pop_front():删除双端队列中最前一个元素
void pop_back():删除双端队列中最后一个元素
void clear():清空双端队列中最后一个元素
iterator begin():返回双端队列头指针，指向第一个元素
iterator end():返回指向双端队列中最后一个元素下一个元素的指针（不包含在双端队列中）
bool empty() const:双端队列是否为空，若true,则双端队列中无元素
Int size() const:返回双端队列中元素的个数
int max_size() const:返回最大可允许的双端队列元素数量值
```

### priority_queue
定义于头文件`<queue>`  

优先队列容器与队列一样，只能**从队尾插入**元素，从**队首删除**元素。

但是它有一个特性，就是队列中优先级最高的元素位于队首，所以出队时，并非按照先进先出的原则进行，而是将当前队列中优先级最高的元素出队。

元素的比较规则默认按元素值由小到大排序，但值最大的在队首。可以重载“<”操作符来重新定义比较规则。	

#### 时间复杂度
- 创建
- 插入
- 删除

#### 基本操作
```c++
empty() // 如果队列为空返回真
pop()  // 删除队顶元素
push() // 加入一个元素
size() // 返回优先队列中拥有的元素个数
top()  // 返回优先队列队顶元素（队列中的front()变成了top()）
```
**在默认的优先队列中，优先级高的先出队。在默认的int型中先出队的为较大的数。**

#### 实例
1. 普通方式：
```c++
priority_queue<int> q;  // 通过操作，按照元素从大到小的顺序出队
priority_queue<int,vector<int>,less<int> > q;  // 与上面效果等同
priority_queue<int,vector<int>,greater<int> > q;  // 通过操作，按照元素从小到大的顺序出队
```
2. **自定义优先级**：
```c++
struct cmp {     
    operator bool ()(int x, int y)     
    {        
        return x > y; // x小的优先级高       //也可以写成其他方式，如： return p[x] > p[y];表示p[i]小的优先级高
    }
};
priority_queue<int, vector<int>, cmp>q;    //定义方法
//其中，第二个参数为容器类型。第三个参数为比较函数。
```
3. 结构体声明方式
```c++
struct node {     
    int x, y;     
    friend bool operator < (node a, node b)     
    {         
        return a.x > b.x;    //结构体中，x小的优先级高     
    }
};
priority_queue<node>q;   //定义方法
//在该结构中，y为值, x为优先级。
//通过自定义operator<操作符来比较元素中的优先级。
//在重载”<”时，最好不要重载”>”，可能会发生编译错误
```

### pair
```c++
// 声明
pair<int, int> p1;
pair<int, int> p2(5, 3);
// 赋值
p1 = make_pair(1, 2);
p1 = pair(1, 2);
// 取第一、第二个元素
int a, b;
a = p1.first  //
b = p1.second  // 
```