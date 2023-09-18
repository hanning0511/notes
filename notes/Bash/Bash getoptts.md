
Tags: #Bash #getopts
Refs: https://eklitzke.org/using-local-optind-with-bash-getopts

# getopts

## 在脚本中直接使用 getopts

```bash
# parse options
while getopts ":f:hx" opt; do
  case $opt in
    f) foo="$OPTARG" ;;
    h) echo "$0 [-h] [-f FOO] [-x] FILE..."; exit;;
    x) set -x ;;
    \?) echo "Invalid option: -$OPTARG" >&2 ;;
  esac
done

# shift so that $@, $1, etc. refer to the non-option arguments
shift "$((OPTIND-1))"
```

在这个例子中，我们有一个接受标志 `-h` 和 `-x` 的脚本，同时也接受带参数的选项 `-f`。该脚本还接受任意数量的位置参数。为了访问位置参数，我们调用 `shift "$((OPTIND-1))"`，这样可以确保 `$@` 等指的是位置参数而不是选项参数。

## 在函数中使用 getopts

在函数中使用 `getopts` 时，需要把 `OPTIND` 设置为本地变量，否则 `getopts` 无法正常工作。

`OPTIND` 默认情况下是一个全局变量。当使用 `getopts` 时，`OPTIND` 被用来标记最后解析选项的 index。在函数中必须把它设置为本地变量，否则当重复调用函数时，`OPTIND` 的值将一直增加，无法反映正确的选项 index。

```bash
myfunc() {
	local OPTIND

	while getopts ":f:hx" opt; do
		case $opt in
			f) foo="OPTARG"
				;;
			h)
				echo "echo "$0 [-h] [-f FOO] [-x] FILE..."; exit"
				;;
			x) set -x
				;;
			?)
				echo "Invalid option: -$OPTARG" >&2
				;;
		esac
	done
}
```