---
title: Python生成器，你了解多少
comments: true
cover: cover.png
date: 2021-05-12 17:48:55
categories:
- Python
keyword: 生成器
tags:
- 原创
---

在Python中，生成器是一种特殊的迭代器。它们是一种用于创建迭代器的函数，在调用时不会立即执行，而是返回一个生成器对象。生成器对象可以按需逐个生成值，而不是一次性生成所有值。
<!-- more -->
# 生成器

## 生成器相对于其他方式更高效

- 内存占用：生成器在迭代过程中一次只生成一个值，并且不会一次性生成所有值。这使得生成器在处理大量数据时，内存占用更低。可以通过比较生成器和其他方式在相同数据量下的内存占用情况来验证这一点。
- 延迟计算：生成器采用延迟计算的方式，只在需要时才会计算生成下一个值。相比于一次性计算所有值，这种延迟计算可以提高效率，在处理大量数据或复杂计算时尤为明显。可以通过比较生成器和其他方式在执行时间上的差异来验证这一点。
- 迭代效率：生成器是一种特殊的迭代器，它的执行过程更接近于原生的迭代器实现。相比于使用列表等容器进行迭代，生成器能够减少循环和条件判断等操作，从而提高迭代效率。可以通过比较生成器和其他方式在迭代过程中的执行速度来验证这一点。
- 空间复杂度：由于生成器是按需生成值，不需要保存生成的所有值，因此其空间复杂度通常比一次性生成所有值的方式更低。可以通过比较生成器和其他方式在处理大规模数据时的空间占用情况来验证这一点。

## 内存比较
```python
import sys

def count_size(func):
    def wrap(*args, **kwargs):
        ret = func(*args, **kwargs)
        print(func.__name__, sys.getsizeof(ret))
        return ret
    return wrap

@count_size
def test_1():
    array = [x for x in range(100)]
    return array


@count_size
def test_2():
    array = (x for x in range(100))
    return array

test_1()   #920
test_2()   #112
```

## 延迟计算
```python
import time

def count_time(func):
    def wrap(*args, **kwargs):
        start = time.time()
        ret = func(*args, **kwargs)
        end = time.time()
        print(func.__name__, (end - start)*1000)
        return ret
    return wrap

@count_time
def test_1():
    array = [num for num in range(1, 1000000) if num % 2 == 0]
    return [num ** 2 for num in array]

def even_numbers():
    num = 2
    while num <= 1000000:
        yield num
        num += 2

@count_time
def test_2():
    array = even_numbers()
    return [num ** 2 for num in array]

test_1()   #237
test_2()   #199
```

## 如何构造生成器

### 生成器函数
```python
def even_numbers():
    num = 2
    while num <= 1000000:
        yield num
        num += 2
 
array =even_numbers()
print(array)   #<generator object even_numbers at 0x1016c2820>
```

### 生成器表达式
```python
array=(x for x in range(10))
print(array)   #<generator object <genexpr> at 0x102871820>
```

### yield from
```python
def my_generator():
    yield from [x for x in range(10)]

new_array=my_generator()
print(new_array)   #<generator object my_generator at 0x10c02e820>
```

