Tags: #Python #List #Sort

# Python 中排序相关问题

## 列表原地排序

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