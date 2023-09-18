Tags: #Python #list #sort #sorted

# Python 中排序相关问题

## 原地排序

```python
a = [1, 4, 2, 5]
a.sort()
print(a)
```

```shell
[1, 2, 4, 5]
```

原地排序不会产生新的变量，排序过后的值仍然保存在原来的变量中。如果需要保留原列表的顺序，则不能用原地排序。

## 使用 `sorted` 对列表排序

```python
a = [1, 4, 2, 5]
b = sorted(a)
print(a)
print(b)
```

```shell
[1, 4, 2, 5]
[1, 2, 4, 5]
```

使用 python 的内建方法 `sorted` 对列表进行排序，排序后的列表被存放到新的变量中。

## Sort Key

`sort` 或者 `sorted` 支持通过 `key` 关键字参数指定排序使用的值，`key` 是一个可执行对象，通常是一个 `lambda` 函数，下面是一些例子 ：  
```python
dl = [
	  {
		  "name": "Tom",
		  "age": 19
	  },
	  {
		  "name": "Jack",
		  "age":: 24
	  }
]

dl.sort(key=lambda d: d["name"])
```

## reserve

`sort` 或者 `sorted` 支持通过 `reserve` 关键字参数对排序结果进行调转： 

```python
dl = [
	  {
		  "name": "Tom",
		  "age": 19
	  },
	  {
		  "name": "Jack",
		  "age":: 24
	  }
]

dl.sort(key=lambda d: d["name"], reverse=True)
```