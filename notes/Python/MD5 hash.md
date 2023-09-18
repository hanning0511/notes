```python
import hashlib

result = hashlib.md5(b'abcdefg')

print(f"The hex digest of hash is: {result.hexdigest()}")
```