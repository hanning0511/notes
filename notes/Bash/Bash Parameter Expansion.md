Tags: #Bash
Refs: [Shell Parameter Expansion](https://www.gnu.org/software/bash/manual/html_node/Shell-Parameter-Expansion.html)

# Parameter Exapnsion

Parameter expansion 的基本形式是 `${parameter}`。

## ${parameter:-word}
如果 parameter 没有被赋值或者值为 null, 则扩展后的值为 word，否则扩展后的值是 parameter 本身。

```shell
$ var=123
$ echo ${var:-unset}
123
```

## ${parameter:=word}
如果 parameter 没有被赋值或者值为 null，则 word 会被赋值给 parameter，位置参数和特殊参数不可以使用这种方法赋值。

```shell
$ var=
$ : ${var:=DEFAULT}
$ echo $var
DEFAULT
```

## ${parameter:?word}
如果 parameter 没有被赋值或者值为 null，则 word 会被输出到 stderr 和 shell 中。如果是非交互式 shell, 则退出。否则 parameter 将会被赋值为 word。

```shell
$ var=
$ : ${var:?var is unset or null}
bash: var: var is unset or null
```

## ${parameter:+word}
如果 parameter 没有被赋值或者值为 null，则什么都不做，否则 parameter 被赋值为 word。

```shell
$ var=123
$ echo ${var:+var is set and not null}
var is set and not null
```

## ${parameter:offset}
## ${parameter:offset:length}
子字符串扩展。

