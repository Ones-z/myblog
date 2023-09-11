---
title: 神奇的语法糖
comments: true
cover: cover.png
date: 2023-09-11 10:45:26
categories:
- Python
keywords: 神奇,语法糖
tags:
- 原创
---

# 精简却功能强大的语法糖

## 三元表达式
> 在Python中，三元表达式的形式为 value_if_true if condition else value_if_false。这种表达式的作用是根据条件的真假选择两个值中的一个作为整个表达式的结果。
> 使用三元表达式可以简化某些条件判断的语法，并且可以在一行中完成相应的逻辑操作。这在某些情况下可以提高代码的可读性和简洁性。

### 缩写
```python
x=5
print(2 if x>2 esle x)   #2
```

### 布尔索引
```python
a,b=10,20
print((b, a) [a < b])   #20
```

### 列表推导式
```python
x=10
print([i for i in range(x)])   #[0,1,2,3,4,5,6,7,8,9]
```

### 字典推导式
```python
x=5
print({i:i+1 for i in range(x)})   #{0: 1, 1: 2, 2: 3, 3: 4, 4: 5}
```

### 生成器
```python
x=5
print((i:i+1 for i in range(x)))   #<generator object <genexpr> at 0x101ed0820>
```

### 在字典应用
```python
age = 22
person_info = {
    "name": "John",
    "age": age,
    "category": "Adult" if age >= 18 else "Child"
}
print(person_info)   # {'name': 'John', 'age': 22, 'category': 'Adult'}
```

### 嵌套条件判断
```python
a, b = 10, 20
print ("1" if a == b else "2" if a > b else "3")   #3
```

### 结合lambda
```python
x=5
print((lambda x:x+1 if x>4 else x)(x))   #6
```

### 结合print
```python
x=5
print(x) if x >4 else print(x-1)   #5
```
