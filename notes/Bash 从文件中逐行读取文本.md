Tags: #Bash 

# Bash 从文件中逐行读取文本

```bash
#!/usr/bin/env bash

file="/path/to/file_to_read"

while IFS= read -r line; do
	echo $line
done < "$file"
```

- 传递给 `read` 命令的 `-r` 选项可以防止反斜杠转义被解释。
- `IFS=` 的作用是防止行前面的空格或结尾的空格被删除。换句话说，加上 `IFS=` 之后，行首和行尾的空格会被自动去掉。