---
title: 异步队列，让你的程序不阻塞
comments: true
cover: cover.png
date: 2023-01-09 10:10:37
categories:
- Fastapi
keyword: 队列,实时日志
tags:
- 原创
---
队列常用于实现生产者-消费者模式。生产者向队列中放入数据，消费者从队列中取出数据进行处理。队列提供了一个缓冲区，使得生产者和消费者可以异步执行，实现解耦和高效处理。
<!-- more -->

# 队列

## 同步模式
```python
import datetime
import queue

mq = queue.Queue()


class RealTimeLogging:
    @staticmethod
    def put(item: str, level="INFO"):
        mq.put(f"{datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')}[{level}]: {item}\n")

    @staticmethod
    def debug(item: str):
        RealTimeLogging.put(item, level="DEBUG")

    @staticmethod
    def info(item: str):
        RealTimeLogging.put(item, level="INFO")

    @staticmethod
    def warning(item: str):
        RealTimeLogging.put(item, level="WARNING")

    @staticmethod
    def error(item: str):
        RealTimeLogging.put(item, level="ERROR")

    @staticmethod
    def critical(item: str):
        RealTimeLogging.put(item, level="CRITICAL")

    @staticmethod
    def get(count: int):
        data = [mq.get_nowait() for _ in range(count) if not mq.empty()]
        [mq.task_done() for _ in data]
        return data
```

## 异步模式

```python
import asyncio
import datetime

mq = asyncio.Queue()


class RealTimeLogging:
    @staticmethod
    async def put(item: str, level="INFO"):
        await mq.put(f"{datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S')}[{level}]: {item}\n")

    @staticmethod
    async def debug(item: str):
        await RealTimeLogging.put(item, level="DEBUG")

    @staticmethod
    async def info(item: str):
        await RealTimeLogging.put(item, level="INFO")

    @staticmethod
    async def warning(item: str):
        await RealTimeLogging.put(item, level="WARNING")

    @staticmethod
    async def error(item: str):
        await RealTimeLogging.put(item, level="ERROR")

    @staticmethod
    async def critical(item: str):
        await RealTimeLogging.put(item, level="CRITICAL")

    @staticmethod
    async def get(count: int):
        data = [mq.get_nowait() for _ in range(count) if not mq.empty()]
        [mq.task_done() for _ in data]
        return data

```

## 生产者

```python
async def runSmoke(config: list):
    # 遍历配置
    for _case in config:
        # 处理前置条件
        if _case["setup"]:
            for setup in _case["setup"]:
                await RealTimeLogging.info(f'''前置条件: "{setup}"''')
                try:
                    _case["Mysql"]._edit(setup)
                except Exception as e:
                    await RealTimeLogging.error(e)
                    await RealTimeLogging.error("SQL发生错误，前置处理失败\n")
```

## 消费者

```python
@router.get("/real-time", summary="返回队列")
async def message(count: int = 10):
    data = await RealTimeLogging.get(count)
    return BaseResponse.success(data)
```

## 客户端调用

```javascript
const [log, setLog] = useState('')

const readRealTime = () => {
  realTimeLog.current = setInterval(() => {
    realTime(10).then((res) => {
      res.map((item) => {
        setLog((prevData) => prevData + item)
      })
    })
  }, 500)
}
return(
  <pre>{log}</pre>
)
```
