---
title: 依赖注入，给你的代码润色
comments: true
cover: cover.png
date: 2022-04-02 11:52:02
categories:
- Fastapi
keyword: 依赖注入,解耦
tags:
- 原创
---

# 依赖注入
> 依赖注入是指在编程中，为了保证功能的使用，先导入或声明所需依赖，如子函数、数据库连接等。
> 依赖注入是一种设计模式，用于降低程序组件之间的耦合度。它通过将组件之间的依赖关系从代码中分离出来，使得组件可以更加灵活地被替换、修改或重用。

## 使用依赖注入的优势
提高代码的复用率：
- 共享数据库连接
- 增强安全、认证和角色管理
- 响应数据注入，可以在原来的响应数据基础上再做出更改，如抽出公用函数，在公用函数中对数据处理

## 如何使用依赖注入

### 添加到函数

#### 函数作为依赖
```python
async def common_parameters(q: Optional[str] = None, page: int = 1, limit: int = 100):
    return {"q": q, "page": page, "limit": limit}

@app.get("/dependency01")
async def dependency01(commons: dict = Depends(common_parameters)):
    return commons
```

#### 类作为依赖
```python
fake_items_db = [{"item_name": "Foo"}, {"item_name": "Bar"}, {"item_name": "Baz"}]


class CommonQueryParams:
    def __init__(self, q: Optional[str] = None, page: int = 1, limit: int = 100):
        self.q = q
        self.page = page
        self.limit = limit


@app.get("/classes_as_dependencies")
# async def classes_as_dependencies(commons: CommonQueryParams = Depends(CommonQueryParams)):
# async def classes_as_dependencies(commons: CommonQueryParams = Depends()):
async def classes_as_dependencies(commons=Depends(CommonQueryParams)):
    response = {}
    if commons.q:
        response.update({"q": commons.q})
    items = fake_items_db[commons.page: commons.page + commons.limit]
    response.update({"items": items})
    return response
```

#### 多层嵌套依赖
```python
def query(q: Optional[str] = None):
    return q

def sub_query(q: str = Depends(query), last_query: Optional[str] = None):
    if not q:
        return last_query
    return q

@app05.get("/sub_dependency")
async def sub_dependency(final_query: str = Depends(sub_query, use_cache=True)):
    return {"sub_dependency": final_query}
```
use_cache默认是True, 表示当多个依赖有一个共同的子依赖时，每次request请求只会调用子依赖一次，多次调用将从缓存中获取

#### 路径操作装饰器依赖
```python
async def verify_token(x_token: str = Header(...)):
    """没有返回值的子依赖"""
    if x_token != "aaa":
        raise HTTPException(status_code=400, detail="X-Token header invalid")

async def verify_key(x_key: str = Header(...)):
    """有返回值的子依赖，但是返回值不会被调用"""
    if x_key != "bbb":
        raise HTTPException(status_code=400, detail="X-Key header invalid")
    return x_key

@app05.get("/dependency_in_path_operation", dependencies=[Depends(verify_token), Depends(verify_key)])  # 这时候不是在函数参数中调用依赖，而是在路径操作中
async def dependency_in_path_operation():
    return [{"user": "user01"}, {"user": "user02"}]
```

#### yield依赖和子依赖
```python
async def get_db_AsyncSession() -> AsyncSession:
    async with AsyncSessionLocal() as AsyncSession:
        yield AsyncSession

DB: AsyncSession = Depends(get_db_AsyncSession)

@router.get("/remove/{id}", summary="删除应用")
async def delApp(id: int, db=DB):
    data = await appCrud.remove(db, id)
    return BaseResponse.success(data)
```

### 添加到组件
```python
app = APIRouter(dependencies=[Depends(verify_token), Depends(verify_key)])
```

### 添加到主程序
```python
app = FastAPI(dependencies=[Depends(verify_token), Depends(verify_key)]）
```