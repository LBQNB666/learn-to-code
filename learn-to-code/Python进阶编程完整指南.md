# 🚀 Python 进阶编程教程

> 面向对象编程、装饰器、异步编程完整指南

## 📂 目录

1. [面向对象编程 (OOP)](#面向对象编程-oop)
2. [类与对象](#类与对象)
3. [继承与多态](#继承与多态)
4. [特殊方法](#特殊方法)
5. [装饰器](#装饰器)
6. [异步编程 asyncio](#异步编程-asyncio)

---

## 🎯 面向对象编程 (OOP)

### 什么是 OOP？

面向对象编程是一种编程范式，用"对象"来组织代码。

### 三大特性

| 特性 | 说明 |
|------|------|
| **封装** | 隐藏内部实现细节 |
| **继承** | 实现代码复用 |
| **多态** | 同一操作不同表现 |

---

## 📦 类与对象

### 1. 定义类

```python
class Dog:
    """狗类"""
    
    # 类属性（所有实例共享）
    species = "Canis familiaris"
    
    # 初始化方法
    def __init__(self, name, age):
        # 实例属性（每个对象独有）
        self.name = name
        self.age = age
    
    # 实例方法
    def bark(self):
        return f"{self.name} says: Woof!"
    
    def description(self):
        return f"{self.name} is {self.age} years old"
```

### 2. 创建对象

```python
# 创建实例
dog1 = Dog("Buddy", 3)
dog2 = Dog("Lucy", 5)

# 访问属性
print(dog1.name)    # Buddy
print(dog2.age)     # 5

# 调用方法
print(dog1.bark())  # Buddy says: Woof!
```

### 3. self 是什么？

`self` 指向对象本身，类似于其他语言的 `this`。

```python
class Person:
    def __init__(self, name):
        self.name = name  # 将 name 存储在当前对象中
    
    def greet(self):
        return f"Hello, I am {self.name}"

p = Person("张三")
print(p.greet())  # Hello, I am 张三
```

---

## 🔗 继承与多态

### 1. 单继承

```python
# 父类
class Animal:
    def __init__(self, name):
        self.name = name
    
    def speak(self):
        return "Some sound"

# 子类继承父类
class Dog(Animal):
    def speak(self):
        return f"{self.name} says: Woof!"

class Cat(Animal):
    def speak(self):
        return f"{self.name} says: Meow!"

# 使用
dog = Dog("Buddy")
cat = Cat("Kitty")

print(dog.speak())  # Buddy says: Woof!
print(cat.speak())  # Kitty says: Meow!
```

### 2. super() 调用父类方法

```python
class Student(Person):
    def __init__(self, name, age, grade):
        # 调用父类的 __init__
        super().__init__(name, age)
        self.grade = grade
    
    def introduce(self):
        # 调用父类的方法
        base_intro = super().greet()
        return f"{base_intro}, I am in grade {self.grade}"
```

### 3. 多态

```python
def make_them_speak(animals):
    """同一个函数，不同对象不同行为"""
    for animal in animals:
        print(animal.speak())

animals = [Dog("Buddy"), Cat("Kitty"), Dog("Max")]
make_them_speak(animals)
# Buddy says: Woof!
# Kitty says: Meow!
# Max says: Woof!
```

---

## ✨ 特殊方法

### 常用特殊方法

| 方法 | 作用 |
|------|------|
| `__init__` | 初始化对象 |
| `__str__` | str() 调用时的输出 |
| `__repr__` | repr() 调用时的输出 |
| `__len__` | len() 调用 |
| `__getitem__` | 索引访问 |
| `__call__` | 对象像函数一样调用 |

### 示例

```python
class Person:
    def __init__(self, name, age):
        self.name = name
        self.age = age
    
    def __str__(self):
        return f"Person({self.name}, {self.age})"
    
    def __repr__(self):
        return f"Person('{self.name}', {self.age})"
    
    def __len__(self):
        return len(self.name)
    
    def __call__(self):
        return f"{self.name} says Hello!"

p = Person("张三", 25)

print(str(p))        # Person(张三, 25)
print(repr(p))       # Person('张三', 25)
print(len(p))        # 2
print(p())           # 张三 says Hello!
```

---

## 🎨 装饰器

### 什么是装饰器？

装饰器可以在不修改原函数的情况下，给函数添加新功能。

### 基础装饰器

```python
def my_decorator(func):
    """装饰器函数"""
    def wrapper(*args, **kwargs):
        print("函数开始执行")
        result = func(*args, **kwargs)
        print("函数执行结束")
        return result
    return wrapper

@my_decorator
def say_hello(name):
    print(f"Hello, {name}!")
    return "OK"

# 等同于: say_hello = my_decorator(say_hello)

result = say_hello("张三")
# 输出:
# 函数开始执行
# Hello, 张三!
# 函数执行结束
```

### 带参数的装饰器

```python
def repeat(times):
    """带参数的装饰器工厂"""
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator

@repeat(3)
def say_hi():
    print("Hi!")

say_hi()
# 输出: Hi! Hi! Hi!
```

### 常用装饰器场景

#### 1. 计时器

```python
import time

def timer(func):
    def wrapper(*args, **kwargs):
        start = time.time()
        result = func(*args, **kwargs)
        end = time.time()
        print(f"{func.__name__} 执行耗时: {end - start:.2f}秒")
        return result
    return wrapper

@timer
def slow_function():
    time.sleep(2)
    print("完成!")

slow_function()
```

#### 2. 记忆化 (Memoization)

```python
def memoize(func):
    """缓存函数结果，加速重复计算"""
    cache = {}
    
    def wrapper(*args, **kwargs):
        key = str(args) + str(kwargs)
        if key not in cache:
            cache[key] = func(*args, **kwargs)
            print(f"计算并缓存: {key}")
        else:
            print(f"使用缓存: {key}")
        return cache[key]
    
    return wrapper

@memoize
def fibonacci(n):
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)

print(fibonacci(10))  # 首次计算
print(fibonacci(10))  # 使用缓存
```

#### 3. 登录验证

```python
def login_required(func):
    def wrapper(*args, **kwargs):
        if not hasattr(wrapper, 'logged_in'):
            print("请先登录!")
            return
        return func(*args, **kwargs)
    return wrapper

@login_required
def view_profile():
    print("查看个人资料")

view_profile()  # 请先登录!
```

---

## ⚡ 异步编程 asyncio

### 为什么需要异步？

异步可以在等待 IO（如网络请求）时同时执行其他任务。

### 基础概念

| 概念 | 说明 |
|------|------|
| **协程 (Coroutine)** | async def 定义的函数 |
| **事件循环 (Event Loop)** | 管理协程执行 |
| **Task** | 协程的封装 |

### async/await 基础

```python
import asyncio

async def say_hello():
    """协程函数"""
    print("Hello!")
    await asyncio.sleep(1)  # 模拟等待
    print("World!")

async def main():
    await say_hello()

# 运行
asyncio.run(main())
```

### 并发执行多个任务

```python
import asyncio
import time

async def task(name, seconds):
    print(f"{name} 开始")
    await asyncio.sleep(seconds)
    print(f"{name} 完成")
    return f"{name} 结果"

async def main():
    # 创建多个任务
    tasks = [
        asyncio.create_task(task("任务A", 2)),
        asyncio.create_task(task("任务B", 1)),
        asyncio.create_task(task("任务C", 3)),
    ]
    
    # 等待所有任务完成async def main():
    # 创建多个任务
    tasks = [
        asyncio.create_task(task("任务A", 2)),
        asyncio.create_task(task("任务B", 1)),
        asyncio.create_task(task("任务C", 3)),
    ]
    
    # 等待所有任务完成
    results = await asyncio.gather(*tasks)
    print(f"\n所有任务结果: {results}")

asyncio.run(main())
```

### 实际应用：异步 HTTP 请求

```python
import asyncio
import aiohttp

async def fetch_url(session, url):
    """异步获取网页"""
    async with session.get(url) as response:
        return await response.text()

async def fetch_all(urls):
    """并发获取多个网页"""
    async with aiohttp.ClientSession() as session:
        tasks = [fetch_url(session, url) for url in urls]
        results = await asyncio.gather(*tasks)
        return results

# 使用
urls = [
    'https://api.github.com',
    'https://api.github.com/users',
    'https://api.github.com/repos'
]

results = asyncio.run(fetch_all(urls))
print(f"获取了 {len(results)} 个页面")
```

### 安装 aiohttp

```bash
pip install aiohttp
```

---

## 📚 综合实例：文件处理类

```python
import os
from datetime import datetime

class FileHandler:
    """文件处理器类"""
    
    def __init__(self, directory):
        self.directory = directory
        self.files = []
        self.scan()
    
    def scan(self):
        """扫描目录中的文件"""
        self.files = []
        for filename in os.listdir(self.directory):
            filepath = os.path.join(self.directory, filename)
            if os.path.isfile(filepath):
                stat = os.stat(filepath)
                self.files.append({
                    'name': filename,
                    'path': filepath,
                    'size': stat.st_size,
                    'modified': datetime.fromtimestamp(stat.st_mtime)
                })
    
    def __len__(self):
        return len(self.files)
    
    def __getitem__(self, index):
        return self.files[index]
    
    def filter_by_extension(self, ext):
        """按扩展名过滤"""
        return [f for f in self.files if f['name'].endswith(ext)]
    
    def sort_by_size(self, reverse=False):
        """按大小排序"""
        return sorted(self.files, key=lambda x: x['size'], reverse=reverse)
    
    def total_size(self):
        """计算总大小"""
        return sum(f['size'] for f in self.files)

# 使用
handler = FileHandler('C:/Users')
print(f"文件数量: {len(handler)}")
print(f"总大小: {handler.total_size()} MB")

# 按大小排序
for f in handler.sort_by_size(reverse=True)[:5]:
    print(f"  {f['name']}: {f['size'] / 1024 / 1024:.2f} MB")
```

---

## 🔄 更新日志

- **2026-04-21**: 创建 Python 进阶编程教程
- 面向对象编程基础
- 继承与多态
- 特殊方法
- 装饰器
- 异步编程 asyncio
