# 📝 Python Logging 日志教程

> 完整的日志配置和使用指南

## 📂 目录

1. [为什么需要日志](#为什么需要日志)
2. [日志级别](#日志级别)
3. [基础配置](#基础配置)
4. [进阶配置](#进阶配置)
5. [实战案例](#实战案例)

---

## ❓ 为什么需要日志？

### print vs logging

| 方面 | print() | logging |
|------|---------|---------|
| 灵活性 | 差 | 强 |
| 日志级别 | 无 | DEBUG/INFO/WARNING/ERROR |
| 输出位置 | 仅控制台 | 控制台/文件/网络 |
| 生产环境 | 不推荐 | 推荐 |

---

## 📊 日志级别

| 级别 | 数值 | 用途 |
|------|------|------|
| DEBUG | 10 | 调试信息 |
| INFO | 20 | 一般信息 |
| WARNING | 30 | 警告信息 |
| ERROR | 40 | 错误信息 |
| CRITICAL | 50 | 严重错误 |

---

## 🔧 基础配置

### 1. basicConfig 快速配置

```python
import logging

# 配置日志
logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    filename='app.log',
    filemode='a'  # 'a' 追加, 'w' 覆盖
)

# 使用
logging.debug("调试信息")
logging.info("一般信息")
logging.warning("警告信息")
logging.error("错误信息")
```

### 2. 格式化符号

| 符号 | 说明 |
|------|------|
| `%(name)s` | Logger 名称 |
| `%(levelname)s` | 日志级别 |
| `%(message)s` | 日志消息 |
| `%(asctime)s` | 时间 |
| `%(filename)s` | 文件名 |
| `%(lineno)d` | 行号 |
| `%(funcName)s` | 函数名 |

---

## ⚙️ 进阶配置

### 完整配置示例

```python
import logging
from logging.handlers import RotatingFileHandler

# 创建 logger
logger = logging.getLogger('my_app')
logger.setLevel(logging.DEBUG)

# 创建格式化器
formatter = logging.Formatter(
    '%(asctime)s - %(name)s - %(levelname)s - %(filename)s:%(lineno)d - %(message)s'
)

# 文件处理器 (带日志轮转)
file_handler = RotatingFileHandler(
    'app.log',
    maxBytes=10*1024*1024,  # 10MB
    backupCount=5
)
file_handler.setLevel(logging.DEBUG)
file_handler.setFormatter(formatter)

# 控制台处理器
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.INFO)
console_handler.setFormatter(formatter)

# 添加处理器
logger.addHandler(file_handler)
logger.addHandler(console_handler)

# 使用
logger.debug("调试信息")
logger.info("一般信息")
logger.warning("警告信息")
logger.error("错误信息")
```

---

## 🎯 实战案例

### 1. 配置文件方式

```python
# logging_config.py
import logging.config

LOGGING_CONFIG = {
    'version': 1,
    'disable_existing_loggers': False,
    'formatters': {
        'standard': {
            'format': '%(asctime)s - %(name)s - %(levelname)s - %(message)s'
        },
        'detailed': {
            'format': '%(asctime)s - %(name)s - %(levelname)s - %(filename)s:%(lineno)d - %(message)s'
        }
    },
    'handlers': {
        'console': {
            'class': 'logging.StreamHandler',
            'level': 'INFO',
            'formatter': 'standard',
            'stream': 'ext://sys.stdout'
        },
        'file': {
            'class': 'logging.handlers.RotatingFileHandler',
            'level': 'DEBUG',
            'formatter': 'detailed',
            'filename': 'app.log',
            'maxBytes': 10485760,  # 10MB
            'backupCount': 5
        }
    },
    'loggers': {
        'my_app': {
            'level': 'DEBUG',
            'handlers': ['console', 'file']
        }
    },
    'root': {
        'level': 'INFO',
        'handlers': ['console']
    }
}

# 应用配置
logging.config.dictConfig(LOGGING_CONFIG)

# 获取 logger
logger = logging.getLogger('my_app')
```

### 2. 多模块日志

```python
# main.py
import logging
import module_a
import module_b

logging.basicConfig(
    level=logging.DEBUG,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s'
)

logger = logging.getLogger(__name__)
logger.info("程序开始")

module_a.do_something()
module_b.do_something()

logger.info("程序结束")
```

```python
# module_a.py
import logging

logger = logging.getLogger(__name__)

def do_something():
    logger.debug("Module A 开始")
    # 业务逻辑
    logger.debug("Module A 完成")
```

### 3. 异常日志记录

```python
import logging

logger = logging.getLogger(__name__)

def divide(a, b):
    try:
        return a / b
    except Exception as e:
        logger.exception("除法出错: %s", e)
        # 或
        logger.error("除法出错: %s", exc_info=True)
        raise

# 使用
try:
    result = divide(10, 0)
except:
    pass  # 日志已经记录
```

---

## 🔄 更新日志

- **2026-04-21**: 创建 Logging 教程
