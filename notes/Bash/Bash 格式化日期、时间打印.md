Tags: #Bash 
Refs: https://www.tutorialkart.com/bash-shell-scripting/bash-date-format-options-examples/

# 格式化日期、时间打印

在 Bash 中，可以通过 `date` 命令打印时间信息。

```shell
$ date
2022年 10月 12日 星期三 09:46:36 CST
```

通过在 `date` 命令后追加格式化选项，可以按需打印想要的时间格式。

```shell
$ date +<format-option><format-option>..
```

如果格式化选项中间包含空格，需要把这个格式化选项发在引号中，单引号和双引号都可以

```shell
$ date '+<format-option> <format-option>'
```

