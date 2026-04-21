# 🔧 Python 迭代器、生成器与上下文管理器

> Python 高级特性详解

## 📂 目录

1. [迭代器 (Iterator)](#迭代器-iterator)
2. [生成器 (Generator)](#生成器-generator)
3. [上下文管理器 (Context Manager)](#上下文管理器-context-manager)
4. [实际应用案例](#实际应用案例)

---

## 🔄 迭代器 (Iterator)

### 什么是迭代器？

迭代器是一个可以**记住遍历位置**的对象，只能往前不能后退。

### 迭代器协议

必须实现两个方法：

| 方法 | 作用 |
|------|------|
| `__iter__()` | 返回迭代器对象本身 |
| `__next__()` | 返回下一个元素，没有时抛出 StopIteration |

### 自定义迭代器

```python
class MyIterator:
    """自定义迭代器：倒序遍历"""
    
    def __init__(self, data):
        self.data = data
        self.index = len(data)  # 从末尾开始
    
    def __iter__(self):
        return self
    
    def __next__(self):
        if self.index <= 0:
            raise StopIteration
        self.index -= 1
        return self.data[self.index]

# 使用
numbers = [1, 2, 3, 4, 5]
it = MyIterator(numbers)

for num in it:
    print(num)
# 输出: 5, 4, 3, 2, 1
```

### for 循环原理

```python
# 普通 for 循环
fruits = ["apple", "banana", "orange"]
for fruit in fruits:
    print(fruit)

# 等价于手动迭代
fruit_iter = iter(fruits)  # 调用 __iter__()
while True:
    try:
        fruit = next(fruit_iter)  # 调用 __next__()
        print(fruit)
    except StopIteration:
        break
```

### 内置迭代器函数

```python
# enumerate - 带索引的迭代
fruits = ["apple", "banana", "orange"]
for i, fruit in enumerate(fruits):
    print(f"{i}: {fruit}")

# zip - 并行迭代
names = ["张三", "李四", "王五"]
ages = [25, 30, 28]
for name, age in zip(names, ages):
    print(f"{name} - {age}岁")

# iter + callable - 迭代直到满足条件
import itertools

def counter():
    n = 0
    while True:
        n += 1
        yield n

count_it = counter()
for _ in range(5):
    print(next(count_it))  # 1, 2, 3, 4, 5
```

---

## ⚡ 生成器 (Generator)

### 什么是生成器？

生成器是一种特殊的迭代器，使用 `yield` 关键字返回值，**不会一次性把所有数据加载到内存**。

### 生成器函数

```python
def my_generator():
    """简单的生成器函数"""
    print("生成第 1 个值")
    yield 1
    print("生成第 2 个值")
    yield 2
    print("生成第 3 个值")
    yield 3

# 使用
gen = my_generator()
print(next(gen))  # 输出: 生成第 1 个值, 1
print(next(gen))  # 输出: 生成第 2 个值, 2
print(next(gen))  # 输出: 生成第 3 个值, 3
# next(gen) 会抛出 StopIteration
```

### 生成器表达式（类似列表推导式）

```python
# 列表推导式 - 一次性生成所有数据
squares_list = [x**2 for x in range(1000000)]  # 占用大量内存

# 生成器表达式 - 按需生成
squares_gen = (x**2 for x in range(1000000))  # 只占用少量内存

# 使用
for square in squares_gen:
    if square > 100:
        break
    print(square)
```

### 生成器优点

| 优点 | 说明 |
|------|------|
| **节省内存** | 不需要一次性加载所有数据 |
| **惰性计算** | 只在需要时才生成值 |
| **无限序列** | 可以表示无限数据流 |

### 实用生成器示例

```python
# 1. 读取大文件
def read_large_file(filepath):
    """逐行读取大文件，不会一次性加载全部"""
    with open(filepath, 'r', encoding='utf-8') as f:
        for line in f:
            yield line.strip()

# 使用
for line in read_large_file('huge_log.txt'):
    if 'ERROR' in line:
        print(line)

# 2. 斐波那契数列
def fibonacci(n):
    """生成前 n 个斐波那契数"""
    a, b = 0, 1
    for _ in range(n):
        yield a
        a, b = b, a + b

# 使用
for num in fibonacci(10):
    print(num)  # 0, 1, 1, 2, 3, 5, 8, 13, 21, 34

# 3. 无限序列
def infinite_counter(start=0):
    """无限计数器"""
    n = start
    while True:
        yield n
        n += 1

# 使用
counter = infinite_counter(1)
for _ in range(5):
    print(next(counter))  # 1, 2, 3, 4, 5
```

---

## 📂 上下文管理器 (Context Manager)

### 什么是上下文管理器？

上下文管理器用于管理资源的获取和释放，确保资源在使用后正确清理。

### 经典写法 vs with 语句

```python
# 经典写法（容易忘记关闭）
file = open('test.txt', 'r')
content = file.read()
file.close()  # 容易忘记！

# with 语句（自动关闭）
with open('test.txt', 'r') as file:
    content = file.read()
# 文件自动关闭
```

### 类实现上下文管理器

```python
class FileManager:
    """文件管理器"""
    
    def __init__(self, filepath, mode):
        self.filepath = filepath
        self.mode = mode
        self.file = None
    
    def __enter__(self):
        """进入上下文时调用"""
        print(f"打开文件: {self.filepath}")
        self.file = open(self.filepath, self.mode)
        return self.file
    
    def __exit__(self, exc_type, exc_value, traceback):
        """退出上下文时调用"""
        print(f"关闭文件: {self.filepath}")
        if self.file:
            self.file.close()
        # 返回 False 异常会继续抛出，返回 True 异常被吞掉
        return False

# 使用
with FileManager('test.txt', 'r') as f:
    content = f.read()
    print(content)
```

### @contextmanager 装饰器

使用 `contextlib` 模块更简洁地创建上下文管理器：

```python
from contextlib import contextmanager

@contextmanager
def file_manager(filepath, mode):
    """用装饰器创建上下文管理器"""
    file = open(filepath, mode)
    try:
        yield file  # 相当于 __enter__ 返回值
    finally:
        file.close()  # 相当于 __exit__

# 使用
with file_manager('test.txt', 'r') as f:
    content = f.read()
    print(content)
```

### 实用示例

```python
from contextlib import contextmanager
import time

# 1. 计时器
@contextmanager
def timer(name):
    """测量代码执行时间"""
    start = time.time()
    yield
    end = time.time()
    print(f"{name} 耗时: {end - start:.2f}秒")

with timer("数据处理"):
    time.sleep(1)
    print("处理中...")

# 2. 临时目录
import tempfile
import shutil

@contextmanager
def temporary_directory():
    """创建临时目录"""
    temp_dir = tempfile.mkdtemp()
    try:
        yield temp_dir
    finally:
        shutil.rmtree(temp_dir)

with temporary_directory() as tmpdir:
    print(f"临时目录: {tmpdir}")
    # 在这里处理临时文件

# 3. 数据库连接
@contextmanager
def db_transaction(connection):
    """数据库事务"""
    try:
        yield connection
        connection.commit()
        print("事务提交")
    except Exception as e:
        connection.rollback()
        print(f"事务回滚: {e}")
        raise

# 使用
# with db_transaction(db) as conn:
#     conn.execute("INSERT ...")
```

---

## 🎯 实际应用案例

### 案例 1### 案例 1: 分批处理大数据

```python
def batch_process(items, batch_size=100):
    """分批处理大量数据"""
    batch = []
    for item in items:
        batch.append(item)
        if len(batch) >= batch_size:
            yield batch
            batch = []
    if batch:
        yield batch  # 返回最后一批

# 使用：处理 10000 条数据
data = range(10000)
for batch in batch_process(data, batch_size=1000):
    print(f"处理 {len(batch)} 条数据")
    # 在这里处理每批数据
```

### 案例 2: 日志分析器

```python
def log_analyzer(filepath, error_only=False):
    """分析日志文件"""
    with open(filepath, 'r', encoding='utf-8') as f:
        for line in f:
            line = line.strip()
            if error_only and 'ERROR' not in line:
                continue
            yield {
                'timestamp': line[:19] if len(line) > 19 else '',
                'level': 'ERROR' if 'ERROR' in line else 'INFO',
                'message': line
            }

# 使用
for log in log_analyzer('app.log', error_only=True):
    print(f"[{log['timestamp']}] {log['message']}")
```

### 案例 3: 资源清理上下文管理器

```python
from contextlib import contextmanager

@contextmanager
def managed_resource(name, resource_type='file'):
    """通用的资源管理器"""
    print(f"[{name}] 获取 {resource_type} 资源")
    
    # 模拟资源获取
    resource = {'id': id({}), 'type': resource_type}
    
    try:
        yield resource
    finally:
        print(f"[{name}] 释放 {resource_type} 资源")
        # 清理代码
        resource = None

# 使用
with managed_resource("数据库连接", "mysql") as conn:
    print(f"使用 {conn['type']} 连接，ID: {conn['id']}")

with managed_resource("文件锁", "lock") as lock:
    print(f"获取 {lock['type']} 锁，ID: {lock['id']}")
```

---

## 📊 迭代器 vs 生成器 vs 列表

| 特性 | 列表 | 迭代器 | 生成器 |
|------|------|--------|--------|
| 内存占用 | 高 | 低 | 低 |
| 计算时机 | 立即 | 按需 | 按需 |
| 方向 | 无限制 | 单向 | 单向 |
| 重复使用 | 可以 | 一次 | 一次 |
| 适用场景 | 小数据 | 遍历控制 | 大数据/无限序列 |

---

## 🔄 更新日志

- **2026-04-21**: 创建迭代器、生成器、上下文管理器教程
- 详细讲解迭代器协议
- 生成器函数和表达式
- 类和装饰器创建上下文管理器
- 实用案例
