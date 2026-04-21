# 🚀 FastAPI Web 开发教程

> 现代 Python 高性能 Web 框架

## 📂 目录

1. [FastAPI 简介](#fastapi-简介)
2. [安装和快速开始](#安装和快速开始)
3. [路由和参数](#路由和参数)
4. [请求体和响应](#请求体和响应)
5. [数据库集成](#数据库集成)
6. [认证和授权](#认证和授权)

---

## ⚡ FastAPI 简介

### 为什么选择 FastAPI？

| 特性 | 说明 |
|------|------|
| **高性能** | 与 Node.js 和 Go 相当 |
| **自动文档** | 自动生成 OpenAPI/Swagger 文档 |
| **类型安全** | 完全类型注解支持 |
| **异步支持** | 原生 async/await |
| **数据验证** | Pydantic 自动验证 |

### 安装

```bash
pip install fastapi uvicorn
```

---

## 🎯 快速开始

### 第一个 FastAPI 应用

```python
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
def read_root():
    return {"message": "Hello, FastAPI!"}

@app.get("/items/{item_id}")
def read_item(item_id: int, q: str = None):
    return {"item_id": item_id, "q": q}

if __name__ == "__main__":
    import uvicorn
    uvicorn.run(app, host="127.0.0.1", port=8000)
```

### 运行服务器

```bash
# 方式 1: 命令行
uvicorn main:app --reload

# 方式 2: 代码中
python main.py
```

### 访问

- 主页: http://127.0.0.1:8000
- 自动文档: http://127.0.0.1:8000/docs
- 备用文档: http://127.0.0.1:8000/redoc

---

## 🛤️ 路由和参数

### 路径参数

```python
@app.get("/users/{user_id}")
def get_user(user_id: int):
    return {"user_id": user_id}

# 类型约束
@app.get("/items/{item_id}")
def get_item(item_id: str):
    return {"item_id": item_id}
```

### 查询参数

```python
@app.get("/search")
def search(q: str = None, limit: int = 10):
    return {"q": q, "limit": limit}

# 必填参数（没有默认值）
@app.get("/calculate")
def calculate(a: int, b: int):
    return {"result": a + b}
```

### 请求体参数

```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    description: str = None
    price: float
    quantity: int = 0

@app.post("/items")
def create_item(item: Item):
    return {"item": item, "message": "创建成功"}
```

---

## 📦 请求体和响应

### 多个数据源

```python
from typing import Optional

@app.put("/users/{user_id}")
def update_user(
    user_id: int,           # 路径参数
    name: str,              # 查询参数
    item: Item,             # 请求体
    q: Optional[str] = None # 可选查询参数
):
    return {
        "user_id": user_id,
        "name": name,
        "item": item,
        "q": q
    }
```

### 响应模型

```python
from typing import List
from pydantic import BaseModel

class User(BaseModel):
    id: int
    name: str
    email: str

class UserList(BaseModel):
    users: List[User]
    total: int

@app.get("/users", response_model=UserList)
def get_users():
    users = [
        User(id=1, name="张三", email="zhangsan@example.com"),
        User(id=2, name="李四", email="lisi@example.com"),
    ]
    return UserList(users=users, total=len(users))
```

### HTTP 状态码

```python
from fastapi import status

@app.post("/items", status_code=status.HTTP_201_CREATED)
def create_item(item: Item):
    return item

@app.get("/notfound", status_code=status.HTTP_404_NOT_FOUND)
def not_found():
    return {"message": "资源不存在"}
```

---

## 🗄️ 数据库集成

### 使用 SQLAlchemy

```bash
pip install sqlalchemy
```

### 数据库配置

```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

# 数据库 URL (SQLite 为例)
DATABASE_URL = "sqlite:///./test.db"

engine = create_engine(DATABASE_URL, connect_args={"check_same_thread": False})
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)
Base = declarative_base()

# 定义模型
class User(Base):
    __tablename__ = "users"
    
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)
    email = Column(String, unique=True, index=True)

# 创建表
Base.metadata.create_all(bind=engine)

# 获取数据库会话
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

### CRUD 操作

```python
from fastapi import Depends, HTTPException
from sqlalchemy.orm import Session

# 获取用户
@app.get("/users/{user_id}", response_model=User)
def get_user(user_id: int, db: Session = Depends(get_db)):
    user = db.query(User).filter(User.id == user_id).first()
    if not user:
        raise HTTPException(status_code=404, detail="用户不存在")
    return user

# 创建用户
@app.post("/users/", response_model=User, status_code=status.HTTP_201_CREATED)
def create_user(user: User, db: Session = Depends(get_db)):
    db_user = db.query(User).filter(User.email == user.email).first()
    if db_user:
        raise HTTPException(status_code=400, detail="邮箱已存在")
    
    new_user = User(name=user.name, email=user.email)
    db.add(new_user)
    db.commit()
    db.refresh(new_user)
    return new_user

# 更新用户
@app.put("/users/{user_id}", response_model=User)
def update_user(user_id: int, user: User, db: Session = Depends(get_db)):
    db_user = db.query(User).filter(User.id == user_id).first()
    if not db_user:
        raise HTTPException(status_code=404, detail="用户不存在")
    
    db_user.name = user.name
    db_user.email = user.email
    db.commit()
    db.refresh(db_user)
    return db_user

# 删除用户
@app.delete("/users/{user_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_user(user_id: int, db: Session = Depends(get_db)):
    user = db.query(User).filter(User.id == user_id).first()
    if not user:
        raise HTTPException(status_code=404, detail="用户不存在")
    
    db.delete(user)
    db.commit()
    return None
```

---

## 🔐 认证和授权

### 简单 API Key 认证

```python
from fastapi import Security, HTTPException
from fastapi.security import APIKeyHeader

api_key_header = APIKeyHeader(name="X-API-Key")

async def verify_api_key(api_key: str = Security(api_key_header)):
    if api_key != "secret-api-key":
        raise HTTPException(status_code=403, detail="无效的 API Key")
    return api_key

@app.get("/protected")
def protected_route(api_key: str = Security(verify_api_key)):
    return {"message": "访问受保护的资源", "api_key": api_key}
```

### Bearer Token 认证

```python
from fastapi import Depends, HTTPException, status
from fastapi.security import HTTPBearer, HTTPAuthorizationCredentials

security = HTTPBearer()

def verify_token(credentials: HTTPAuthorizationCredentials = Security(security)):
    token = credentials.credentials
    if token != "valid-token":
        raise HTTPException(
            status_code=status.HTTP_401_UNAUTHORIZED,
            detail="无效的 Token"
        )
    return token

@app.get("/secure-data")
def secure_data(token: str = Depends(verify_token)):
    return {"data": "敏感信息", "token": token}
```

---

## 📁 完整项目结构

```
project/
├── main.py              # FastAPI 应用入口
├── models.py            # 数据库模型
├── schemas.py           # Pydantic 模型
├── database.py          # 数据库配置
├── routers/
│   ├── __init__.py
│   ├── users.py         # 用户路由
│   └── items.py        # 物品路由
├── auth.py              # 认证
└── requirements.txt     # 依赖
```

---

## 🔄 更新日志

- **2026-04-21**: 创建 FastAPI Web 开发教程
- 安装和快速开始
- 路由和参数
- 请求体和响应
- 数据库集成 (SQLAlchemy)
- 认证和