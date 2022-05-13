---
tags: [programming language]
title: Rust
created: '2022-05-13T09:22:31.202Z'
modified: '2022-05-13T09:25:06.011Z'
---

# Rust

## 基础知识

### 常用集合类型

#### Vector

##### 创建 Vector

```Rust
let v: Vec<i32> = Vec::new();
```

这里使用了类型标注，因为没有向这个 Vector 中存入任何值， Rust 并不知道我们想要存储什么类型的元素，所以需要类型标注。我们也可以使用初始值来创建一个 Vector, Rust 会根据初始值来推断元素的数据类型，这时就不需要类型标注了。比如

```Rust
let v = vec![1, 2, 3];
```

这里 ```vec!``` 是 Rust 提供的宏，用于通过初始值来创建 Vector.

##### 更新 Vector

更新 Vector 的值，可以使用 ```push()``` 方法。当然，前提是被更新的 Vector 必须是可变类型。

```Rust
let mut v = vec![1, 2, 3];
v.push(4);
```

##### 读取 Vector 元素

读取 Vector 元素有两种方法，一种是通过索引，一种是通过 ```get()``` 方法。

```Rust
let v = vec![1, 2, 3, 4, 5];

let third: &i32 = &v[2];
println!("The third element is {}", third);

match v.get(2) {
    Some(third) => println!("The third element is {}", third),
    None => println!("There is no third element."),
}
```

这里有两个需要注意的地方。首先，我们使用索引值 2 来获取第三个元素，索引值是从 0 开始的。其次，这两种不同的获取第三个元素的方法分别是

- 使用 ```&``` 和 ```[]``` 返回一个引用。
- 使用 ```get()``` 方法，以索引作为参数，返回一个 ```Option<&T>```。

第二种 ```get()``` 方法的好处在于，程序可以选择如何处理当索引值在 Vector 中没有对应值的情况。



一旦程序获取了一个有效引用，借用检查器将会执行所有权和借用规则来确保 Vector 内容的这个引用和任何其他引用保持有效。还记得吗，在相同的作用域内，不能同时存在可变引用和不可变引用的规则。当我们获取了 Vector 的一个元素的不可变引用，并尝试在 Vector 末尾增加一个元素，这是行不通的, 所以下面这段代码在编译是会报错

```Rust
let mut v = vec![1, 2, 3, 4, 5];
let first = &v[0];
v.push(6)；
```

为什么=第一个元素的引用会关心 Vector 结尾的变化呢？设想一种情况，在 Vector 结尾添加新元素是，如果刚好没有足够的空间来将所有的元素依次相邻存放，这时候就需要分配新内存并将原来的所有元素拷贝到新的内存中。这时，第一个元素的索引就指向了被释放的内存。借用规则不会允许程序进入这种状况。

##### 遍历 Vector 中的元素

- 依次访问 Vector 中的各个元素的引用

```Rust
let v = vec![1, 2, 3, 4];
for i in &v {
    println!("{}", i);
}
```

- 依次访问 Vector 中的个元素的可变引用，并修改其值

```Rust
let mut v vec![1, 2, 3, 4];
for i in &mut v {
    *i += 1;
}
```

#### HashMap

##### 创建 HashMap

```Rust
use std::collections::HashMap;
let mut map = HashMap::new();
```

##### 向 HashMap 中插入字段

```Rust
map.insert(String::from("name"), String::from("Jack"));
```

##### 访问 HashMap 中的字段

通过 `get()` 方法访问 HashMap 中的字段，如果所要访问的字段存在则返回 `Some<T>` ,否则返回 `None`。

```Rust
match map.get("name") {
    Some(n) => pirntln!("name is {}", n),
    None => println!("name not found")
}
```

##### 更新 HashMap

###### 覆盖一个值

如果我们插入一键值对，接着用相同的键插入一个不同的值，与这个键相关联的旧值将被替换。

```Rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Blue"), 25)
```

###### 只在键没有对应值时插入

```Rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);

scores.entry(String::from("Yellow")).or_insert(50);
scores.entry(String::from("Blue")).or_insert(50);
```

###### 根据旧值更新一个值

```Rust
use std::collections::HashMap;

let text = "hello world wonderful world";

let mut map = HashMap::new();

for word in text.split_whitespace() {
    let count = map.entry(word).or_insert(0);
    *count += 1;
}
```



### 结构体 (struct)

#### 定义结构体

```Rust
struct User {
  name: String,
  age: u8,
}
```

#### 创建结构体实例

- 基本用法
```Rust
let user = User {
  name: String::from("Jack"),
  age: 20,
};
```

- 简写法, 可以省略字段名
```Rust
let user = User {
  String::from("Jack"),
  20，
}
```

- 从其他实例继承部分字段
```Rust
let user1 = User {
  name: String::from("Tome");
  ..user
}
```

#### 更新结构体实例字段值

```Rust
user.age = 22;
```

#### 元组结构体 （tuple struct)

> 元组结构体的没有字段名，但是要定义每个字段的数据类型

- 定义元组结构体

```Rust

struct Color(i32, i32, i32);
```

- 创建元组结构体实例

```Rust
let black = Color(0, 0, 0);
```

- 访问元组结构体字段

```Rust
println!("first value of black is {}", black.0);
```

## 其他主题

### 从标准输入中读入信息

#### 读取字符串

```Rust
use std::io;

fn main() {
    println!("Input your name:");
    
    let mut name = String::new();
    io::stdin()
    	.read_line(&mut name)
    	.expect("fail to read name");
    let name = name.trim();
    
    println!("Hello {}!", name);
}
```

#### 读取数字

```rust
use std::io;

fn main() {
    println!("Input your age:");
    
    let mut age = String::new();
    io::stdin()
    	.read_line(&mut age)
    	.expect("fail to read age");
    let age: u8 = age.trim().parse().expect("fail to parse age");
    
    println!("You're {} year's old", age);
}
```

#### 读取密码

[rpassword](https://github.com/conradkleinespel/rpassword)

```Rust
use rpassword;

let password = rpassword::read_password().unwrap();
println!("Your password is {}", password);
```



### 命令行参数解析

- [clap](https://github.com/clap-rs/clap) - **Command Line Argument Parser for Rust**

### 序列化

- [serde](https://github.com/serde-rs/serde) - **a framework for serializing and deserializing Rust data structures efficiently and generically**

### 文件读写

#### 读取文本文件

```Rust
use std::fs;

fn main() {
    let text = fs::read_to_string("hello.txt").expect("something is wrong while reading the file");
    println!("{}", text);
}
```

### HTTP Client

#### [reqwest](https://github.com/seanmonstar/reqwest)

> An ergonomic, batteries-included HTTP Client for Rust.

##### Blocking Client

```
[dependencies]
reqwest = { version = "0.11", features = ["blocking", "json"] }
```

```Rust
use std::collections::HashMap;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let resp = reqwest::blocking::get("https://httpbin.org/ip")?
        .json::<HashMap<String, String>>()?;
    println!("{:#?}", resp);
    Ok(())
}
```

### HTML Document 解析

#### [scraper](https://crates.io/crates/scraper)

> HTML parsing and querying with CSS selectors.

### 执行 Shell 命令

#### 基本使用方法

```Rust
use std::process::Command;

let cmd = Command::new("sh")
				.arg("-c")
				.arg("echo hello")
				.output()
				.expect("fail to execute process");
let stdout = String::from_utf8(cmd.stdout).unwrap();
let stderr = String::from_utf8(cmd.stderr).unwrap();
let code = cmd.status.code().unwrap();
```

---

## 代码片段

### 下载文件

```Rust
/// download_file downloads a file from src, which is a URL, to a dest, which is a local path.
pub fn download_file(src: &str, dest: &str) {
    let res = reqwest::blocking::Client::new().get(src).send().unwrap();
    if res.status() != reqwest::StatusCode::OK {
        println!("downloading {} failed - {}", src, res.text().unwrap());
        exit(-1);
    }
    fs::write(dest, res.bytes().unwrap()).unwrap();
}
```

### 获取当前系统的发行版信息

```Rust
pub fn os_release() -> String {
    let cmd = Command::new("sh")
        .args([
            "-c",
            "cat /etc/os-release | grep -E ^ID= | awk -F= '{print $2}' | tr -d '\"'",
        ])
        .output()
        .expect("spawn new process failed");
    if cmd.status.code().unwrap() != 0 {
        String::from("")
    } else {
        String::from_utf8(cmd.stdout).unwrap()
    }
}
```

### 使用 base64 对字符串编码

```Rust
pub fn base64(origin: &str) -> String {
    let cmd_str = format!("echo {} | base64", origin);
    let cmd = Command::new("sh")
        .args(["-c", &cmd_str])
        .output()
        .expect("base64 failed");
    if cmd.status.code().unwrap() != 0 {
        let err = String::from_utf8(cmd.stderr).unwrap();
        panic!("{}", err);
    }
    String::from_utf8(cmd.stdout).unwrap()
}
```


