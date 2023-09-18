Flags: #Python #CSV
Refs: [CSV File Reading and Writing](https://docs.python.org/3/library/csv.html)

# Python CSV 文件操作

## 读取 CSV 文件

```python
import csv

with open("data.csv", newline='') as csvfile:
	reader = csv.reader(csvfile, delimiter=",", quotechar="|")
	for row in reader:
		print(", ".join(row))
```