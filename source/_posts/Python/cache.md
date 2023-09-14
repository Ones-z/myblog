---
title: 你考虑过使用函数运算缓存吗
comments: true
cover: cover.png
date: 2021-11-20 18:31:57
categories:
- Python
keyword: 运算缓存,性能
tags:
- 原创
---

对那些需要高频率以相同参数运行的函数，或者需要重复加载的数据，如果每次都执行函数完整的代码，或者重复从文件加载数据进行处理，会试程序运行效率变低。改善效率可通过使用内存缓存或磁盘缓存的形式，从而提高代码效率。
<!-- more -->

# 函数运算缓存
> 函数运算缓存是一种优化技术，用于在函数被多次调用时缓存函数的计算结果，避免重复计算相同的输入。这可以提高函数的性能，尤其是当函数的计算开销较大时。

## 如何使用

### lru_cache
functools.lru_cache装饰器可以接受以下参数：
- maxsize：指定缓存的最大大小，表示可以缓存多少个不同的函数调用结果。默认值为128。当缓存达到最大容量时，最近最少使用的结果会被移除，以便为新的结果腾出空间。
- typed：指定是否对不同参数类型的函数调用进行分别缓存。默认值为False，即不区分参数类型。如果设置为True，则会对不同参数类型的函数调用进行分别缓存。

```python
import functools
import time

@functools.lru_cache(maxsize=1)
def fibonacci(n):
    time.sleep(0.1)
    if n <= 1:
        return n
    else:
        return fibonacci(n-1) + fibonacci(n-2)

# 第一次调用会进行计算，并缓存结果
print(fibonacci(6))

# 第二次调用会直接返回缓存的结果
print(fibonacci(6))
```
<font color="red">内置模块，无需安装</font>

### Cachier

cachier装饰器可以接受以下参数来自定义缓存行为：
- stale_after：指定缓存的过期时间。可以传递一个datetime.timedelta对象，表示时间间隔。默认值为None，表示缓存永不过期。
- cache_dir：指定缓存文件的目录路径。默认为当前工作目录下的.cachier文件夹。
- hash_params：布尔值，指定是否将函数的参数用于计算哈希值。默认为False，表示不使用参数计算哈希值。

```python
import datetime
from cachier import cachier
import time

@cachier(stale_after=datetime.timedelta(seconds=10))
def test(x):
    time.sleep(3)
    return x

print(test(1))

print(test(1))
```
<font color="red">第三方模块，需要安装：pip3 install cachier</font>

### Cachetools
cachetools是另一个Python库，它提供了用于缓存的工具类和装饰器。与cachier不同，cachetools库提供了更灵活的缓存策略和高级功能。

TTLCache是cachetools库提供的一个基于时间的缓存实现。在创建TTLCache对象时，可以传递以下参数来自定义缓存的行为：
- maxsize：指定缓存最多可以存储的项数。默认值为None，表示缓存没有大小限制。
- ttl：指定缓存项的存活时间，单位为秒。默认值为None，表示缓存项没有存活时间限制。
- timer：用于获取当前时间的函数。默认值为time.time，使用系统的计时器。
- getsizeof：获取缓存项大小的函数。默认值为sys.getsizeof，根据对象的内存占用进行估计。

```python
import time
from cachetools import TTLCache, cached

@cached(cache=TTLCache(maxsize=100, ttl=60))
def test(x):
    time.sleep(5)
    return x

print(test(1))

print(test(1))
```
<font color="red">第三方模块，需要安装：pip3 install cachetools></font>