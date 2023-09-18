生成一个子进程并将 **stdout** 和 **stderr** 重定向到同一个文件。它使用与 [[Rust 执行管道连接的外部命令]] 相同的方法，但是 `process::Stdio` 写到一个指定的文件。`File::try_clone`为 **stdout** 和 **stderr** 引用同一个文件句柄。它将确保两个句柄以相同的游标位置写入。

下面的代码相当于运行 Unix shell 命令 `ls . oops >out.txt 2>&1`。

```Rust
use std::fs::File;
use std::io::Error;
use std::process::{Command, Stdio};

fn main() -> Result<(), Error> {
    let outputs = File::create("out.txt")?;
    let errors = outputs.try_clone()?;

    Command::new("ls")
        .args(&[".", "oops"])
        .stdout(Stdio::from(outputs))
        .stderr(Stdio::from(errors))
        .spawn()?
        .wait_with_output()?;

    Ok(())
}
```