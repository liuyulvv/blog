---
title: '指针与const'
date: 2021-07-07 15:31:39
categories: [C++]
tags: [C++]
description: 理解C++中的指针与const一起使用的场景
---

### const与指针的用法

```cpp
//从右往左读
const int p;            // p is a int const
const int *p;           // p is a pointer to int const
int const *p;           // p is a pointer to const int
int * const p;          // p is a const pointer to int
const int * const p;    // p is a const pointer to int const
int const * const p;    // p is a const pointer to const int
```

1. p is a int const

p是由const修饰的变量，它的值无法改变。

2. p is a pointer to int const

p是一个指向整型常量的指针。

3. p is a pointer to const int

p是一个指向整型常量的指针，和2相同。

4. p is a const pointer to int

p是一个常量指针，指向整型。

5. p is a const pointer to int const

p是一个常量指针，指向整型常量。

6. p is a const pointer to const int

p是一个常量指针，指向整型常量，和5相同。

### 区别

```cpp
const int const_a = 10;   // a是一个const修饰的变量，无法更改a的值
int a = 10, b = 10;

// p是一个指向整型常量的指针
const int *p = &const_a;      
// 可以指向其他对象      
// 允许一个指向整型常量的指针指向一个非常量对象
p = &a;
// *p = 11; // 错误，无法修改

// 常量指针必须初始化
int *const const_p = &a;
// const_p = &b; //错误，常量指针不可以指向其他对象
*const_p = 11;  //正确，常量指针可以修改指针所指对象的值

// 常量指针，指向常量
// 可以指向非常量
// 既不能指向其他对象，也无法修改所指对象的值
const int *const const_p_const = &a;
// *const_p_const = 12;   // 错误，无法修改所指对象的值
// const_p_const = &b;    // 错误，无法指向其他对象
```

### 顶层const和底层const

用名词顶层const表示指针本身是个常量，而用名词底层const表示指针所指的对象是一个常量。更一般的，顶层const可以表示任意的对象是常量，这一点对任何数据类型都使用。底层const则与指针和引用等有关。比较特殊的是，指针既可以是顶层const，也可以是底层const。

```cpp
const int const_a = 10; //无法修改const_a的值，顶层const

int b = 10;
int *const p1 = &b; //无法修改p1的值，即无法指向其他对象，顶层const
const int *p2 = &b; //可以修改p2的值，即可以指向其他对象，底层const
```

### 总结

1. 允许一个指向常量的指针指向一个非常量对象，指针可以指向其他对象，但是无法通过指针修改所指对象的值。

2. 常量指针必须初始化，不可以指向其他对象，可以修改指针所指对象的值。
