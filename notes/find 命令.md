Tags: #Bash #Find

# find 命令

1. 根据名称搜索文件。
```shell
$ find ./GFG -name sample.txt
```

2. 根据通配符搜索文件。
```shell
$ find ./GFG -name *.txt
```

3. 搜索文件并删除文件。
```shell

$ find ./GFG -name sample.txt -exec rm -i {} \;
```

4. 搜索空文件和目录。
```shell
$ find ./GFG -empty
```

5. 按权限搜索文件。
```shell
$ find ./GFG -perm 644
```

6. 搜索时忽略指定目录
```shell
$ find ./GFG -name *.sh -not -path *.git*
```
