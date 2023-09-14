---
title: 特立独行的运算符
comments: true
cover: cover.png
date: 2021-03-24 10:21:26
categories:
- Python
keywords: 高效,运算符
tags:
- 原创
---
# 高效的运算符

## 链式比较

### 并集

```python
i=5
ans=1<i<10
print(ans)    #True
```

### 合集

```python
i=5
ans=10>i<=9
print(ans)    #True
```


## 合并字典
```python
dict1 = {"a": 1, "b": 2}
dict2 = {"c": 3, "d": 4}
 
merged_dict = dict1 | dict2
print(merged_dict)    #{'a': 1, 'b': 2, 'c': 3, 'd': 4}
```
