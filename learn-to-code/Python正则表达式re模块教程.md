# 🎯 Python 正则表达式教程

> re 模块完整指南

## 📂 目录

1. [正则基础](#正则基础)
2. [re 模块函数](#re-模块函数)
3. [正则语法](#正则语法)
4. [常用案例](#常用案例)

---

## 📝 正则基础

### 什么是正则表达式？

正则表达式是一种**匹配字符串**的模式，可以用来搜索、验证、提取数据。

### 导入 re 模块

```python
import re
```

---

## 🔧 re 模块函数

### 1. re.match() - 从开头匹配

```python
# 匹配成功返回 Match 对象，失败返回 None
pattern = r'hello'
text = 'hello world'

result = re.match(pattern, text)
print(result.group())  # hello
print(result.start())  # 0
print(result.end())     # 5
```

### 2. re.search() - 搜索第一个匹配

```python
# 在整个字符串中搜索
pattern = r'world'
text = 'hello world'

result = re.search(pattern, text)
print(result.group())  # world
```

### 3. re.findall() - 查找所有匹配

```python
# 返回所有匹配的列表
pattern = r'\d+'  # 匹配数字
text = '我有 3 个苹果，5 个橘子'

result = re.findall(pattern, text)
print(result)  # ['3', '5']
```

### 4. re.sub() - 替换

```python
# 替换所有匹配
pattern = r'\d+'
text = '我有 3 个苹果，5 个橘子'

result = re.sub(pattern, '很多', text)
print(result)  # 我有 很多 个苹果，很多 个橘子
```

### 5. re.split() - 分割

```python
# 按模式分割
pattern = r'[,\s]+'  # 逗号或空格
text = '苹果,香蕉 橘子;葡萄'

result = re.split(pattern, text)
print(result)  # ['苹果', '香蕉', '橘子', '葡萄']
```

---

## 📋 正则语法

### 基本元字符

| 字符 | 含义 | 示例 |
|------|------|------|
| `.` | 任意字符（除换行） | `a.c` 匹配 abc |
| `\d` | 数字 [0-9] | `\d+` 匹配 123 |
| `\D` | 非数字 | `\D+` 匹配 abc |
| `\w` | 字母数字下划线 | `\w+` 匹配 abc_123 |
| `\W` | 非字母数字 | `\W+` 匹配 @# |
| `\s` | 空白字符 | 空格、\t、\n |
| `\S` | 非空白字符 | | |
| `\n` | 换行 | |
| `\t` | 制表符 | |

### 数量词

| 字符 | 含义 | 示例 |
|------|------|------|
| `*` | 0 次或多次 | `ab*` 匹配 a, ab, abbb |
| `+` | 1 次或多次 | `ab+` 匹配 ab, abbb |
| `?` | 0 次或 1 次 | `ab?` 匹配 a, ab |
| `{n}` | 恰好 n 次 | `\d{3}` 匹配 123 |
| `{n,}` | n 次或多次 | `\d{3,}` 匹配 123, 1234 |
| `{n,m}` | n 到 m 次 | `\d{3,5}` 匹配 123-12345 |

### 字符类

```python
# [] - 匹配括号内的任意字符
[abc]       # 匹配 a, b, 或 c
[a-z]       # 匹配小写字母
[A-Z]       # 匹配大写字母
[a-zA-Z]    # 匹配所有字母
[0-9]       # 匹配数字

# [^] - 否定
[^abc]      # 匹配除了 a, b, c 之外的字符
[^0-9]      # 匹配非数字
```

### 边界匹配

| 字符 | 含义 |
|------|------|
| `^` | 字符串开头 |
| `$` | 字符串结尾 |
| `\b` | 单词边界 |
| `\B` | 非单词边界 |

### 逻辑与分组

| 字符 | 含义 |
|------|------|
| `|` | 或 |
| `()` | 分组 |
| `(?:...)` | 非捕获分组 |
| `\1` | 引用第一个分组 |

---

## 🎯 常用案例

### 1. 验证邮箱

```python
def validate_email(email):
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return re.match(pattern, email) is not None

# 测试
print(validate_email('test@example.com'))  # True
print(validate_email('invalid@'))  # False
```

### 2. 提取 URL

```python
text = '访问 https://example.com 和 http://test.org'

# 提取所有 URL
urls = re.findall(r'https?://[^\s]+', text)
print(urls)  # ['https://example.com', 'http://test.org']
```

### 3. 提取日期

```python
text = '今天是 2026-04-21，明天是 2026-04-22'

# 提取日期 (YYYY-MM-DD)
dates = re.findall(r'\d{4}-\d{2}-\d{2}', text)
print(dates)  # ['2026-04-21', '2026-04-22']

# 提取年月日
pattern = r'(\d{4})-(\d{2})-(\d{2})'
matches = re.findall(pattern, text)
for match in matches:
    year, month, day = match
    print(f'{year}年{month}月{day}日')
```

### 4. 手机号验证

```python
def validate_phone(phone):
    # 中国手机号：1开头，11位数字
    pattern = r'^1[3-9]\d{9}$'
    return re.match(pattern, phone) is not None

print(validate_phone('13812345678'))  # True
print(validate_phone('12345678901'))  # False
```

### 5. 密码强度验证

```python
def validate_password(password):
    # 至少8位，包含大小写字母和数字
    if len(password) < 8:
        return False
    if not re.search(r'[A-Z]', password):
        return False
    if not re.search(r'[a-z]', password):
        return False
    if not re.search(r'\d', password):
        return False
    return True

print(validate_password('Abc12345'))  # True
print(validate_password('abc12345'))  # False
```

### 6. 去除 HTML 标签

```python
html = '<p>这是<strong>加粗</strong>文本</p>'

# 去除所有标签
text = re.sub(r'<[^>]+>', '', html)
print(text)  # 这是加粗文本
```

### 7. 提取数据并分组

```python
log = '2026-04-21 10:30:45 ERROR: 数据库连接失败'

# 分组提取
pattern = r'(\d{4}-\d{2}-\d{2}) (\d{2}:\d{2}:\d{2}) (\w+): (.+)'
match = re.search(pattern, log)

if match:
    date, time, level, message = match.groups()
    print(f'日期: {date}')
    print(f'时间: {time}')
    print(f'级别: {level}')
    print(f'消息: {message}')
```

### 8. 敏感信息脱敏

```python
def mask_phone(phone):
    # 138****5678
    return re.sub(r'(\d{3})\d{4}(\d{4})', r'\1****\2', phone)

def mask_id(id_card):
    # 110***********1234
    return re.sub(r'(\d{3})\d{10}(\d{4})', r'\1**********\2', id_card)

print(mask_phone('13812345678'))  # 138****5678
print(mask_id('110101199001011234'))  # 110***********1234
```

---

## 🔄 更新日志

- **2026-04-21**: 创建正则表达式教程
- re 模块函数
- 正则语法
- 常用案例
