title: C++11 auto类型推导
author: 卓智斌
tags:
  - C++
categories:
  - C++
  - Learning C++11
date: 2018-04-27 14:47:00
---
## 1. 概述

C++ 11引入了auto关键字(之前的C++也有auto，但用处不大)，实现了自动类型推导。在某些情况下方便了复杂类型的获取，简化编码，提高效率。但auto不能滥用，否则可能会给代码的维护性和可读性带来严重的影响。

**建议只在确定只有带来好处，没有坏处的情况下使用auto**

## 2. 基本使用

```cpp
auto x = 5;                 //OK: x是int类型
```
```cpp
auto p = new auto(1);       //OK: p是int*类型
```
auto可以用于new操作符，auto(1)推导出int(1)
```cpp
const auto *v = &x, u = 6;  //OK: v是const int*类型，u是const int类型
```
&x是int* 类型，const auto * 中的auto应该int类型，v被推导出const int * 类型，u被推导为const int类型。  
注意u必须有后面的"=6"，其初始化不能产生二义性(如=6.0)，否则编译错误。  
```cpp
static auto y = 0.0;        //OK: y是double类型
auto int r;                 //ERROR: auto不再表示存储类型指示符
```
```cpp
auto s;                     //ERROR: 无法推导出s的类型
```
由于auto是在编译期推导的，所以无法推出s类型，必须立即初始化。

## 3. 推导规则

```cpp
int x = 0;
auto * a = &x;      //a -> int *, auto -> int
auto b = &x;        //b -> int *, auto -> int *
auto &c = x;        //c -> int &, auto -> int
auto d = c;         //d -> int, auto-> int

const auto e = x;   //e -> const int, auto -> int
auto f = e;         //f -> int, auto -> int

const auto & g = x; //g -> const int &, auto -> int
auto & h = g;       //h -> const int &, auto -> const int
```

- 当不声明为指针或引用时，auto推导结果为，初始化表达式抛弃引用和cv限定符的类型。
- 当声明为指针或引用时，auto推导结果会保持初始化表达式的cv属性。
- auto推导的结果与函数模板参数的自动推导类似。

## 4. 限制

- auto不能用于函数参数
- auto不能用于非静态成员变量
- 无法定义数组

```cpp
int arr[10] = {0};
auto a = arr;       //OK, aa -> int *
auto b[10] = arr;   //ERROR
```

- 无法推导出模板参数

```cpp
std::vector<int> vec;
std::vector<auto> a = vec; //ERROR
```

## 5. 建议

### 5.1 只在确定的情况下使用auto（不限以下几点）

- 简化迭代器代码

```cpp
std::map<double,double> kv;
std::map<double,double>::iterator iter = kv.begin();
for(; iter != kv.end(); ++iter)
......
```

简化成

```cpp
std::map<double,double> kv;
for(auto iter = kv.begin; iter != kv.end(); ++iter)
......
```

有一些迭代器类型会很长，更应该使用auto。



