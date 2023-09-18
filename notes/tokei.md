Tag: #tool

---

# tokio

https://github.com/chinanf-boy/tokei-zh

tokei 是一个统计代码信息的统计程序。

### 安装

```shell
cargo install tokei
```

## 使用方法

### 基本用法

```shell
# 报告 ./foo 和所有子目录代码的统计数据
$ tokei ./foo
```

### 统计多个目录

```shell
$ tokei ./foo ./bar ./baj
```

### 排除文件和目录

tokei 会忽略 `.gitignore` 和`.ignore`文件中的列出的文件和目录， 也可以使用 `--exclude` 来指定要忽略的对象。

```shell
$ tokei ./foo --exclue *.rs
```

### 排序输出

默认情况下，tokei 按语言名称的字母顺序排序，但是支持通过`--sort`来指定排序的方法。
- blanks
- code
- comments
- lines

```shell
$ tokei ./foo --sort code
```

### 输出不同格式

tokei 通常输出为为终端设计的漂亮的人类可读格式. 还有使用 `--output` 选项各种其他格式,对于将数据带入另一个程序更有用.
**注意:** 这个版本的tokei编译时,没有任何序列化格式,以启用序列化,使用features标志重新安装tokei.

```shell
  ALL:
  cargo install tokei --features all

  JSON:
  cargo install tokei --features json

  CBOR:
  cargo install tokei --features cbor

  YAML:
  cargo install tokei --features yaml

  CBOR:
  cargo install tokei --features cbor
```

目前支持的格式
- JSON `--output json`
- YAML `--output yaml`
- TOML `--output toml`
- CBOR `--output cbor`

```shell
$ tokei ./foo --output json
```