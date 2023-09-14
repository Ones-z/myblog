---
title: 解包有哪些骚操作
comments: true
cover: cover.png
date: 2021-03-04 11:08:09
categories:
- Python
keyword: 必须掌握,解包
tags:
- 原创
---

# 解包
> 在 Python 中，解包（Unpacking）是指将可迭代对象（例如列表、元组、集合等）中的元素一一分配给变量的过程。解包可以使得代码更加简洁和可读，并且可以同时对多个变量进行赋值。

## 基本解包
> 将可迭代对象中的元素按顺序赋值给对应数量的变量。
```python
x, y, z = ('a', 'b', 'c')
print(x,y,z)    #'a','b','c'
```

## 扩展解包
> 使用 * 运算符将可迭代对象中的剩余元素解包为一个列表。
```python
x, *y, z = ('a', 'b', 'c', 'd')  # x='a', y=['b', 'c'], z='d'
```

## 忽略特定元素
> 使用 _ 或其他不需要的变量名来标识不关心的元素。
```python
x, *_ = ('a', 'b', 'c')  # x='a'，其余的元素被忽略
```

## 嵌套解包
> 对嵌套的可迭代对象进行解包，可以同时解开多层嵌套。
```python
x, *y, (z, w) = ('a', 'b', 'c', ('d', 'e'))  # x='a', y=['b', 'c'], z='d', w='e'
```

## *args
> *args 将接收解包后的参数作为一个元组，在函数内部可以遍历和处理这些参数。
```python
def point(x, y):
    print(x,y)
foo_list = '12'
point(*foo_list)     #1 2
```

## **kw
> **kw 是一种在函数定义和函数调用中处理关键字参数的语法。它允许你在函数内部以字典的方式访问和操作这些参数。
```python
def point(x, y):
    print(x,y)
foo_list = {'x':1,'y':2}
point(**foo_list)    #1 2
```

