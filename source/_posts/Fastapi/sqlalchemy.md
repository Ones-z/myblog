---
title: SQLAlchemy的异步模式
comments: true
cover: cover.png
date: 2022-08-16 10:02:05
categories:
- Fastapi
keyword: SQLAlchemy,异步模式
tags:
- 原创
---

SQLAlchemy是一个Python编程语言下的开源SQL工具库，它提供了一组高效、灵活和可扩展的API，用于处理关系型数据库。SQLAlchemy提供了面向对象的方式来操作数据库，使得开发人员可以更方便地进行数据库访问和操作，同时也提供了较高的性能和安全性。
<!-- more -->

# SQLAlchemy

## 核心组件

### SQLAlchemy Core
SQLAlchemy Core提供了一个高层次的SQL表达式语言，允许你使用Python语言编写SQL命令，而不需要直接编写原始的SQL语句。这使得代码更清晰、易于维护，并且避免了SQL注入等安全问题。

### ORM（对象关系映射）
SQLAlchemy提供了一个强大的ORM框架，允许你通过定义Python类来表示数据库表和模型之间的映射关系。ORM将数据库的查询、插入、更新和删除等操作转化为面向对象的方法调用，使得开发人员能够以更自然的方式操作数据库，而无需直接编写SQL语句。

### 连接池管理
SQLAlchemy提供了连接池管理功能，可以有效地管理和重用数据库连接，提高数据库查询的性能和效率。

### 数据库适配器
SQLAlchemy支持多种关系型数据库，包括MySQL、PostgreSQL、SQLite、Oracle等，通过不同的数据库适配器，可以将SQLAlchemy用于各种不同类型的数据库。

### 构建异步模式

#### 创建异步连接
```python
import os

from fastapi import Depends
from sqlalchemy.ext.asyncio import AsyncSession, create_async_engine
from sqlalchemy.orm import declarative_base, sessionmaker

from app.utils.configHandler import ConfigHandler

env = "pro" if "APP_ENV" in os.environ and os.environ["APP_ENV"] == "production" else "test"

mysql_url = "mysql+aiomysql://{}:{}@{}:{}/{}?charset=utf8mb4".format(*ConfigHandler.getMysqlConfig(env))

engine = create_async_engine(
    mysql_url,
    # 查看原生语句（未格式化）,禁止终端显示sql日志
    echo=False,
    future=True,
)
Base = declarative_base(bind=engine)

# 创建session元类
AsyncSessionLocal: AsyncSession = sessionmaker(
    class_=AsyncSession, 
    autocommit=False, 
    autoflush=False, 
    expire_on_commit=False, 
    bind=engine
)

async def get_db_AsyncSession() -> AsyncSession:
    async with AsyncSessionLocal() as AsyncSession:
        yield AsyncSession
```

#### 依赖注入
```python
DB: AsyncSession = Depends(get_db_AsyncSession)
```

#### 异步DTO
```python
from typing import Optional

from sqlalchemy import select
from sqlalchemy.ext.asyncio import AsyncSession

from app.models import AppModel

async def getall(
    db: AsyncSession, project_id: Optional[int] = None, app_name: Optional[str] = None, host: Optional[str] = None
):
    filters = []
    if project_id:
        filters.append(AppModel.project_id == project_id)
    if app_name:
        filters.append(AppModel.app_name.like(f"%{app_name}%"))
    if host:
        filters.append(AppModel.host.like(f"%{host}%"))
    data = await db.execute(select(AppModel).where(and_(*filters)).order_by(AppModel.id.desc()))
    return data.scalars().fetchall()
```

#### 异步路由函数
```python
from typing import Optional

from fastapi import APIRouter

from app.crud import appCrud
from app.db.async_mysql import DB
from app.utils.baseResponseHandler import BaseResponse

router = APIRouter(
    prefix="/app",
    tags=["app"],
    # dependencies=[Depends(get_db)],
    responses={404: {"description": "Not found"}},
)


@router.get("/list", summary="获取应用列表")
async def queryApps(
    pageSize: int,
    current: int,
    app_name: Optional[str] = "",
    project_id: Optional[int] = None,
    host: Optional[str] = None,
    db=DB,
):
    _db = await appCrud.getall(db, app_name=app_name, project_id=project_id, host=host)
    return BaseResponse.pagenation(_db[(current - 1) * pageSize : current * pageSize], current, len(_db), pageSize)

```

#### 放置SQL链接超时

```python
engine = create_async_engine(
    mysql_url,
    # echo=False, #查看原生语句（未格式化）,禁止终端显示sql日志
    # future=True, #设置是否使用异步 Future 版本的引擎，默认为 True。如果为 False，则会返回同步版本的引擎。
    max_overflow=5, #指定连接池可容纳的最大溢出连接数，默认为 10。当连接池中的所有连接都被占用且达到 pool_size 时，额外的连接将被创建，直到达到 max_overflow 的限制。
    pool_recycle=600, #设置连接的重置周期（以秒为单位）。当超过指定的时间后，连接将被自动关闭并重新创建，以防止连接长时间闲置或出现问题。
    pool_pre_ping=True, #重新连接的时候会先ping一下
    pool_size=10, #指定数据库连接池的大小，默认为 5。连接池是用于管理数据库连接的，可以控制并发访问数据库的连接数量。
)
```

