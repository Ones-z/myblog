---
title: 深拷贝与浅拷贝，你知道区别吗
comments: true
cover: cover.png
date: 2023-09-11 11:24:09
categories:
- Python
keyword: 深拷贝,浅拷贝
tags:
- 原创
---

# 深拷贝和浅拷贝的区别

## 浅拷贝
> 浅拷贝创建一个新对象，该对象与原始对象共享内部的可变对象的引用。简而言之，浅拷贝只复制了对象的顶层元素，而没有复制内部的嵌套对象。当对嵌套对象进行修改时，原始对象和浅拷贝对象都会受到影响

### 使用copy()函数执行浅拷贝

```python
import copy

list1 = [1, 2, [3, 4]]
list2 = copy.copy(list1)  # 执行浅拷贝

list2[2].append(5)

print(list1)  # 输出: [1, 2, [3, 4, 5]]
print(list2)  # 输出: [1, 2, [3, 4, 5]]
```

### 或使用切片操作符[:]进行浅拷贝
```python
list1 = [1, 2, [3, 4]]
list2 = list1[1:] # 执行深拷贝

list2[1].append(5)

print(list1)  # 输出: [1, 2, [3, 4, 5]]
print(list2)  # 输出: [2, [3, 4, 5]]
```

## 深拷贝
> 深拷贝创建一个新对象，并递归地复制所有嵌套对象，而不仅仅是顶层元素。深拷贝使得原始对象和拷贝对象完全独立，彼此之间的修改互不影响。

### 使用deepcopy()函数可以执行深拷贝

```python
import copy

list1 = [1, 2, [3, 4]]
list2 = copy.deepcopy(list1)  # 执行深拷贝

list2[2].append(5)

print(list1)  # 输出: [1, 2, [3, 4]]
print(list2)  # 输出: [1, 2, [3, 4, 5]]
```


