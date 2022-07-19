[std::process::Command](https://doc.rust-lang.org/std/process/struct.Command.html)

example:

```Rust
use std::process::Command;

let output = if cfg!(target_os = "windows") {
    Command::new("cmd")
            .args(["/C", "echo hello"])
            .output()
            .expect("failed to execute process")
} else {
    Command::new("sh")
            .arg("-c")
            .arg("echo hello")
            .output()
            .expect("failed to execute process")
};

let hello = output.stdout;
```