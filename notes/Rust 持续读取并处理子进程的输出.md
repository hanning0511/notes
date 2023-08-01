在 [[Rust 执行外部命令并处理命令的输出]] 中，处理在外部 `Command` 完成之前不会开始。下面的代码调用 `Stdio::piped` 来创建一个管道，一旦 `BufReader` 被更新，就可以实现连续读取子进程的 **stdout**。

下面的代码等同于 Unix shell 命令 `journalctl | grep usb`。

```Rust
use std::process::{Command, Stdio};
use std::io::{BufRead, BufReader, Error, ErrorKind};

fn main() -> Result<(), Error> {
    let stdout = Command::new("journalctl")
        .stdout(Stdio::piped())
        .spawn()?
        .stdout
        .ok_or_else(|| Error::new(ErrorKind::Other,"Could not capture standard output."))?;

    let reader = BufReader::new(stdout);

    reader
        .lines()
        .filter_map(|line| line.ok())
        .filter(|line| line.find("usb").is_some())
        .for_each(|line| println!("{}", line));

     Ok(())
}
```