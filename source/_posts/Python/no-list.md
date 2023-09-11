---
title: 提升运速，你还在使用列表吗
comments: true
cover: cover.png
date: 2023-09-11 17:24:38
categories:
- Python
keyword: 列表,性能优化
tags:
- 原创
---
<font color="green" size="4">列表是一个动态数组，它的元素在内存中是连续存储的。</font>在迭代过程中，由于连续内存访问的局部性原理，CPU的缓存能够更好地预取和缓存数据，从而提高访问效率。但是，对于列表来说，由于每个元素的大小可能不同，导致在迭代时内存访问的模式变得更加不规则，这可能会降低缓存的效果。
<!-- more -->

# 替代列表迭代方式

## 元组替代列表

### 内存占用
元组在创建后不可修改，因此它们具有固定的大小。相比之下，列表是可变的，可以随时进行添加、删除和修改操作。由于元组的大小固定，它们在内存中占用的空间更小，不需要额外的内存来处理动态调整大小的操作。

### 迭代速度
由于元组不可变，对元组进行迭代通常比对列表进行迭代更快。这是因为解释器可以优化遍历固定大小的元组，而对于可变的列表，则可能需要进行动态的大小调整和重新分配内存等操作。

### 哈希性能
元组是不可变对象，因此它们可以作为字典的键或集合的成员。在计算哈希值时，不可变的元组只需计算一次，并可以重复使用，从而提高了哈希表的性能。相比之下，可变的列表无法作为字典的键或集合的成员，因为它们的哈希值可能会发生变化。

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
    array = [x for x in range(1000)]
    return tuple(array)


@count_size
def test_2():
    array = [x for x in range(1000)]
    return array

test_1()  #8040
test_2()  #8856
```
## 哈希表替代列表

### 查找速度更快
在哈希表中，元素的查找速度通常为 O(1)，而在列表中，元素的查找速度通常为 O(n)，其中 n 是列表的长度。这是因为哈希表使用哈希函数将键映射到特定的位置，从而实现快速的查找。
例如，假设有一个学生名单，我们需要根据学生的学号查找其相关信息。使用哈希表，我们可以将学号作为键，将学生信息作为值存储在哈希表中，通过学号即可快速定位到对应的学生信息。而如果使用列表，则需要遍历整个列表来查找目标学号对应的学生信息。

### 插入和删除的效率更高
在哈希表中，插入和删除元素的时间复杂度通常也为 O(1)。而在列表中，插入和删除元素时，需要移动其他元素来保持顺序，其时间复杂度通常为 O(n)。
例如，假设我们要维护一个任务列表，需要频繁地插入和删除任务。使用哈希表，可以根据任务名称快速定位到对应的任务，并在 O(1) 时间内完成插入或删除操作。而如果使用列表，需要遍历列表来找到目标任务位置，并进行元素的移动操作。

### 高效处理唯一性
哈希表能够高效地处理唯一性要求，即保证存储的元素不重复。通过哈希表的特性，我们可以快速判断某个元素是否已经存在于哈希表中，从而避免重复的操作。
例如，假设我们需要统计一段文本中不同单词的出现次数。使用哈希表，我们可以将每个单词作为键，出现次数作为值，在遍历文本时，即可快速判断该单词是否已经存在于哈希表中，并更新对应的出现次数。而如果使用列表，每当遇到一个新单词时，需要遍历整个列表来判断是否已经存在。

#### 哈希表查询元素

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

array = [x for x in range(10000)]
new_array =dict(enumerate(array))

@count_time
def test_1():
    return array[1000]

@count_time
def test_2():
    return new_array[1000]

test_1()   #0.0021
test_2()   #0.0009
```

#### 哈希表增加元素

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

array = [x for x in range(10000)]
new_array =dict(enumerate(array))

@count_time
def test_1():
    array.append(10001)
    return array

@count_time
def test_2():
    new_array[10001]=10001
    return new_array

test_1()   #0.0021
test_2()   #0.0011
```

#### 哈希表删除元素

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

array = [x for x in range(10000)]
new_array =dict(enumerate(array))

@count_time
def test_1():
    array.remove(1000)
    return array

@count_time
def test_2():
    del new_array[1000]
    return new_array

test_1()   #0.0181
test_2()   #0.0011
```

## 推荐列表推导式

列表推导式比显式的遍历更快速
- 优化的底层实现：列表推导式是在底层进行优化的。Python 解释器使用了一些技巧来提高列表推导式的性能，例如内部的循环展开、迭代器和生成器的使用等。这些优化可以减少中间步骤，提高执行效率。
- 原生的 C 实现：Python 的列表推导式是通过底层的 C 代码来实现的。与用 Python 代码编写的遍历相比，C 代码的执行速度更快，因为 C 是一种编译语言，能够直接转化为机器码并执行。这使得列表推导式能够更高效地执行循环和条件操作，从而提高性能。

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
    new_list=[]
    for x in range(100):
        new_list.append(x)
    return new_list

@count_time
def test_2():
    return [x for x in range(100)]

test_1()  #0.00905
test_2()  #0.00476
```