## 导入 `logging` 模块

```python
import logging
```

## Log Level

`logging` 模块提供了下面几种 log  level.
- DEBUG
- INFO
- WARNING
- ERROR
- CRITICAL

```python
import logging

logging.debug('This is a debug message')
logging.info('This is an info message')
logging.warning('This is a warning message')
logging.error('This is an error message')
logging.critical('This is a critical message')
```

```shell
WARNING:root:This is a warning message
ERROR:root:This is an error message
CRITICAL:root:This is a critical message
```