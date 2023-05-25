## 代码片段

### 读取文本文件的最后N行

```python
import sys
from pathlib import Path


if len(sys.argv) != 3:
    print(
        f"""Usage: python3 {sys.argv[0]} <file/to/read> <n/lines/to/read>
Example: python3 {sys.argv[0]} /etc/os-release 3"""
    )
    exit(1)


file = Path(sys.argv[1])
if not file.is_file():
    print(f"Error: {sys.argv[1]} is not a file!")
    exit(1)

try:
    nlines = int(sys.argv[2])
except:
    print(f"invalid number: {sys.argv[2]}!")
    exit(1)


lines = [line for line in file.read_text().split("\n") if line]

count = 0
if len(lines) > nlines:
    for i in range(len(lines) - nlines - 1, len(lines) - 1):
        print(f"{count} {lines[i]}")
        count += 1
else:
    for line in lines:
        print(f"{count} {line}")
        count += 1
```