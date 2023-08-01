Tags: #Rust #命令行参数
Refs: [clap docs](https://docs.rs/clap/latest/clap/)

# Rust 命令行参数的解析

## 使用 clap

### Cargo.toml 配置

在 `Cargo.toml` 中加入下面配置

```toml
[dependencies]
clap = { version = "3.2.17", features = ["derive"] }
```

如果 Rust 版本高于 1.62.0, 可以使用下面的命令直接添加

```shell
cargo add clap --features derive
```

### 示例

#### 基本用法

```Rust
use clap::Parser;

/// Simple program to greet a person
#[derive(Parser, Debug)]
#[clap(
	author("Han Ning"),
	version("0.1"),
	about("A Commandline utility"),
	long_about = None,
)]
struct Args {
   /// Name of the person to greet
   #[clap(short, long, value_parser)]
   name: String,

   /// Number of times to greet
   #[clap(short, long, value_parser, default_value_t = 1)]
   count: u8,
}

fn main() {
   let args = Args::parse();

   for _ in 0..args.count {
       println!("Hello {}!", args.name)
   }
}
```

```shell
$ demo --help
clap [..]
A simple to use, efficient, and full-featured Command Line Argument Parser

USAGE:
    demo[EXE] [OPTIONS] --name <NAME>

OPTIONS:
    -c, --count <COUNT>    Number of times to greet [default: 1]
    -h, --help             Print help information
    -n, --name <NAME>      Name of the person to greet
    -V, --version          Print version information

$ demo --name Me
Hello Me!
```