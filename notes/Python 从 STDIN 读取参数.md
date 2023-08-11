Tags: #Python #STDIN 

一个 **Python** 脚本，如果需要从 **STDIN** 读取参数并解析成 **JOSN** 格式，可以参考下面的代码：

```python
import json
import sys

params = json.load(sys.stdin)
```