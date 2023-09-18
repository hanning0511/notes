**Leader Key** 是一种扩展 VIM 快捷键功能的方法，通过使用一系列的键来执行一个命令。默认的 **Leader Key** 是反斜杠(\\)。因此，如果你有一个 `<Leader>Q` 的映射，你可以通过输入 `\Q` 来执行该映射对应的命令。

**Leader Key** 不是固定的，可以通过修改变量 `mapleader` 来指定。例如：

```vim
let mapleader = ","
```

上面的配置将使 **Leader Key** 变为逗号（，）。