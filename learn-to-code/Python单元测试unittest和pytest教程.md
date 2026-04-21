# 🧪 Python 单元测试教程

> unittest 和 pytest 完整指南

## 📂 目录

1. [为什么需要单元测试](#为什么需要单元测试)
2. [unittest 入门](#unittest-入门)
3. [pytest 入门](#pytest-入门)
4. [pytest 高级特性](#pytest-高级特性)
5. [实战案例](#实战案例)

---

## ❓ 为什么需要单元测试？

### 测试的好处

| 好处 | 说明 |
|------|------|
| **发现 bug** | 早期发现错误 |
| **重构信心** | 修改代码不破坏功能 |
| **文档作用** | 测试即文档 |
| **回归测试** | 防止旧功能被破坏 |

### 测试金字塔

```
        ┌─────────┐
        │  E2E    │  少量，端到端测试
        ├─────────┤
        │  集成   │  中量，模块间测试
        ├─────────┤
        │  单元   │  大量，快速，隔离
        └─────────┘
```

---

## 🔧 unittest 入门

### 基本结构

```python
import unittest

# 1. 导入 unittest
# 2. 创建测试类，继承 unittest.TestCase
# 3. 编写以 test_ 开头的测试方法
# 4. 使用断言方法验证结果

class TestMathFunctions(unittest.TestCase):
    
    def setUp(self):
        """每个测试前执行"""
        print("准备测试环境")
        self.numbers = [1, 2, 3, 4, 5]
    
    def tearDown(self):
        """每个测试后执行"""
        print("清理测试环境")
    
    def test_add(self):
        """测试加法"""
        self.assertEqual(1 + 1, 2)
        self.assertEqual(1 + (-1), 0)
    
    def test_subtract(self):
        """测试减法"""
        self.assertEqual(5 - 3, 2)
        self.assertEqual(10 - 10, 0)
    
    def test_multiply(self):
        """测试乘法"""
        self.assertEqual(3 * 4, 12)
        self.assertEqual(0 * 100, 0)

if __name__ == '__main__':
    unittest.main()
```

### 常用断言方法

| 方法 | 说明 |
|------|------|
| `assertEqual(a, b)` | a == b |
| `assertNotEqual(a, b)` | a != b |
| `assertTrue(x)` | x is True |
| `assertFalse(x)` | x is False |
| `assertIsNone(x)` | x is None |
| `assertIn(item, list)` | item in list |
| `assertRaises(Error)` | 抛出指定异常 |

### 示例：测试计算器

```python
# calculator.py
def add(a, b):
    return a + b

def subtract(a, b):
    return a - b

def multiply(a, b):
    return a * b

def divide(a, b):
    if b == 0:
        raise ValueError("除数不能为零")
    return a / b
```

```python
# test_calculator.py
import unittest
from calculator import add, subtract, multiply, divide

class TestCalculator(unittest.TestCase):
    
    def test_add(self):
        self.assertEqual(add(2, 3), 5)
        self.assertEqual(add(-1, 1), 0)
        self.assertEqual(add(0, 0), 0)
    
    def test_subtract(self):
        self.assertEqual(subtract(5, 3), 2)
        self.assertEqual(subtract(10, 10), 0)
        self.assertEqual(subtract(0, 5), -5)
    
    def test_multiply(self):
        self.assertEqual(multiply(3, 4), 12)
        self.assertEqual(multiply(0, 100), 0)
        self.assertEqual(multiply(-2, 3), -6)
    
    def test_divide(self):
        self.assertEqual(divide(6, 2), 3)
        self.assertEqual(divide(5, 2), 2.5)
    
    def test_divide_by_zero(self):
        with self.assertRaises(ValueError):
            divide(10, 0)

if __name__ == '__main__':
    unittest.main()
```

### 运行测试

```bash
# 运行所有测试
python -m unittest test_calculator.py

# 运行所有测试（详细模式）
python -m unittest test_calculator.py -v

# 运行指定测试类
python -m unittest test_calculator.TestCalculator

# 运行指定测试方法
python -m unittest test_calculator.TestCalculator.test_add
```

---

## ⚡ pytest 入门

### 安装

```bash
pip install pytest
```

### 基本用法

```bash
# 运行所有测试
pytest

# 详细输出
pytest -v

# 显示打印内容
pytest -v -s

# 停在第一个失败
pytest -x

# 显示局部变量
pytest -l
```

### 测试文件命名

- `test_*.py` - 以 test_ 开头的文件
- `*_test.py` - 以 _test 结尾的文件

### 基本测试

```python
# test_simple.py

def add(a, b):
    return a + b

# 测试函数以 test_ 开头
def test_add():
    assert add(2, 3) == 5
    assert add(-1, 1) == 0
    assert add(0, 0) == 0

def test_add_with_list():
    result = add([1, 2], [3, 4])
    assert result == [1, 2, 3, 4]
```

### 运行

```bash
$ pytest -v

test_simple.py::test_add PASSED
test_simple.py::test_add_with_list PASSED
```

---

## 🎯 pytest 高级特性

### 1. 参数化测试

```python
import pytest

@pytest.mark.parametrize("input,expected", [
    (1, 2),
    (2, 4),
    (3, 6),
    (4, 8),
])
def test_double(input, expected):
    assert input * 2 == expected

# 多参数
@pytest.mark.parametrize("a,b,expected", [
    (1, 2, 3),
    (0, 5, 5),
    (-1, 1, 0),
])
def test_add(a, b, expected):
    assert a + b == expected
```

### 2. Fixtures (夹具)

```python
import pytest

# 创建一个 fixture
@pytest.fixture
def sample_data():
    """提供测试数据"""
    return {
        'name': '张三',
        'age': 25,
        'city': '北京'
    }

# 使用 fixture
def test_user(sample_data):
    assert sample_data['name'] == '张三'
    assert sample_data['age'] == 25

# 带参数的 fixture
@pytest.fixture
def user_builder():
    def build(name, age):
        return {'name': name, 'age': age}
    return build

def test_build_user(user_builder):
    user = user_builder('李四', 30)
    assert user['name'] == '李四'
    assert user['age'] == 30
```

### 3. 共享 Fixture (conftest.py)

```python
# conftest.py
import pytest

@pytest.fixture(scope='session')
def db_connection():
    """会话级 fixture，整个测试会话只创建一次"""
    print("\n创建数据库连接")
    conn = create_db_connection()
    yield conn
    print("\n关闭数据库连接")
    conn.close()

@pytest.fixture
def sample_users(db_connection):
    """依赖另一个 fixture"""
    return db_connection.query("SELECT * FROM users LIMIT 10")
```

### 4. 跳过测试

```python
import pytest

# 跳过测试
@pytest.mark.skip(reason="暂未实现")
def test_future_feature():
    pass

# 条件跳过
@pytest.mark.skipif(sys.version_info < (3, 8), reason="需要 Python 3.8+")
def test_new_feature():
    pass

# 预期失败
@pytest.mark.xfail(reason="已知 bug")
def test_known_bug():
    assert False
```

### 5. Mock 测试

```python
import pytest
from unittest.mock import Mock, patch

def test_mock_function():
    mock_obj = Mock(return_value=42)
    assert mock_obj() == 42
    mock_obj.assert_called_once()

@patch('module.ClassName')
def test_patch_class(mock_class):
    mock_class.return_value.method.return_value = "mocked"
    from module import ClassName
    obj = ClassName()
    assert obj.method() == "mocked"
```

### 6. 异常测试

```python
import pytest

def divide(a, b):
    if b == 0:
        raise ValueError("除数不能为零")
    return a / b

def test_divide_by_zero():
    with pytest.raises(ValueError, match="除数不能为零"):
        divide(10, 0)
```

---

##