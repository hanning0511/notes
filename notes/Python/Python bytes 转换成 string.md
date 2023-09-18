Tags: #Python 
Refs: [convert bytes to string in python](https://www.geeksforgeeks.org/how-to-convert-bytes-to-string-in-python/)

## 使用 `decode()` 方法

该方法适用于使用了特定编码方法的 **bytes**，其中参数字符串被编码是所使用的编码方法。`decode()` 和 `encode()` 互为逆操作。

```python
# Program for converting bytes
# to string using decode()

data = b'GeeksForGeeks'

# display input
print('\nInput:')
print(data)
print(type(data))

# converting
output = data.decode()

# display output
print('\nOutput:')
print(output)
print(type(output))
```

## 使用 `str()` 函数

`str()` 函数返回对象的字符串版本。

```python
# Program for converting bytes to string using decode()
data = b'GeeksForGeeks'

# display input
print('\nInput:')
print(data)
print(type(data))

# converting
output = str(data, 'UTF-8')

# display output
print('\nOutput:')
print(output)
print(type(output))
```

## 使用 `codecs.decode()` 方法

该方法用于将二进制字符串解码为正常字符串。

```python
# Program for converting bytes to string using decode()

# import required module
import codecs

data = b'GeeksForGeeks'

# display input
print('\nInput:')
print(data)
print(type(data))

# converting
output = codecs.decode(data)

# display output
print('\nOutput:')
print(output)
print(type(output))
```