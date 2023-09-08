---
title: 服务端性能工具 - Locust
date: 2023-09-07 14:30:35
tags:
- 原创
categories:
- 性能
comments: true
cover: cover.png
---

如何编写  [locust](https://www.locust.io/)  脚本

# Locust
> Locust 是一个开源负载测试工具。使用 Python 代码定义用户行为，也可以仿真百万个用户。
> Locust 是非常简单易用，分布式，用户负载测试工具。Locust 主要为网站或者其他系统进行负载测试，能测试出一个系统可以并发处理多少用户。
> Locust 是完全基于时间的，因此单个机器支持几千个并发用户。相比其他许多事件驱动的应用，Locust 不使用回调，而是使用轻量级的处理方式 gevent。

## 使用locust

1. 安装locust
``` bash
pip3 install locust
```
2. 定义测试场景
``` python
from locust import HttpUser,TaskSet,task
class my_task(TaskSet):
    wait_time = between(1, 3)
    @task
    def my_task(self):
        url='/testcase/list?current=1&pageSize=10'
        res=self.client.get(url)
        assert res.status_code==200
```
3. 初始化并发策略
``` python
class my_user(HttpUser):
    tasks=[my_task]
    min_wait = 500
    max_wait = 5000
``` 
4. 运行
``` bash
locust -f test.py
```
5. 打开本地页面并输入host
