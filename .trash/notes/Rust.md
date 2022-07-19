---
  tags: [programming language]
title: Rust
created: '2022-05-13T09:22:31.202Z'
modified: '2022-05-13T09:25:06.011Z'
---

# Rust

## 基础知识

### 变量

#### 声明变量

Rust 中使用 `let`关键字来声明一个变量。

```Rust
let name: String;
aget = String::from("Jack");
let age = 18;
```



#### 可变性

默认情况下变量是**不可变的（immutable）**。不过你也可以通过`mut`关键字让变量是**可变的（mutable）**。

- 定义一个不可变变量

```Rust
let x = 6; // x is immutable
x = 7;     // this line will not compile
```

- 定义一个可变的变量

```Rust
let mut x = 6; // x is mutable
x = 7;         // this line can compile
```

#### 常量

Rust 中使用`const`来声明一个常量，并且值的类型必须标注。

与不可变变量类似，**常量（constant）**是绑定到一个常量名且不允许更改的值。

```Rust
const ONE_HOUR_IN_SECONDS: u32 = 60 * 60;
```

Rust 常量的命名约定是全部字母都使用大写，并使用下划线分隔，编译器能够在编译时计算一些有限的操作。

#### 遮蔽（shadow）

在 Rust 中，你可以声明和前面变量具有相同名称的新变量。这种情况下，前面的变量会被后面的变量遮蔽（shadow），这意味着我们再次使用该变量名时访问到的是第二个变量的值。

```Rust
let x = 5;
let x = x + 1;
println!("The value of x is {}", x);
```

遮蔽和将变量标记为`mut`不同，通过遮蔽我们可以对值进行一些类型转换。因为我们再次使用`let`关键字时有效地创建了一个新变量，所以我们可以改变值的类型，且重复使用相同的名称。例如：

```Rust
let spaces = "  ";
let spaces = spaces.len();
```

### 数据类型

Rust 的每个值都有确切的数据类型，要告诉 Rust 数据是什么类型的，从而让 Rust 知道如何处理该数据。

#### 标量类型（scalar）

标量类型标识单个值。Rust 中有 4 个基本的标量类型：整型，浮点型，布尔型和字符。

##### 整数类型

| 长度  | 有符号类型 | 无符号类型 |
| ----- | ---------- | ---------- |
| 8位   | i8         | u8         |
| 16位  | i16        | u16        |
| 32位  | i32        | u32        |
| 64位  | i64        | u64        |
| 128位 | i128       | u128       |
| arch  | isize      | usize      |

`isize`和`usize`类型的取值范围取决于程序运行的计算机体系结构，在表中显示为 arch，若使用 64 位架构系统则为 64 位，若使用 32 位架构系统则为 32 位。

##### 浮点类型

Rust 中浮点类型有两种基本类型： `f32`和`f64`。

##### 布尔类型

Rust 中布尔类型有两个可能值：`true` 和 `false`。

##### 字符类型

Rust 中声明 `char` 型字面量采用单引号，这与字符串字面量不同，字符串字面量使用双引号。Rust 中字符类型大小位 4 个字节，表示一个 Unicode 标量值，这意味着它可以表示的远远不止是 ASCII。

#### 复合类型（compound type）

Rust 有两种基本的复合类型：元组（tuple）和数组（array）。

##### 元组

元组是将多种类型的多个值组合到一个复合类型中的一种基本方式。元组的长度固定，声明后，它的长度无法增长或缩小。

###### 创建元组

```Rust
let tup: (i32, f64, u8) = (500, 6.4, 1);
```

###### 解构元组

```Rust
let (x, y, z) = tup;
```

###### 访问元组元素

```Rust
let x = tup.0;
```

###### 单元类型

没有任何值的元组`()`是一种特殊类型。该类型被称为单元类型（unit type），该值被称为单元值（unit value），如果表达式不返回任何其他值，就隐式地返回单元值。

##### 数组

将多个值组合在一起的另一种方式是数组（array）。与元组不同，数组的每个元素必须具有相同的类型。Rust 中数组具有固定长度。

数组数据会被分配到栈中，而不是堆中。

###### 创建数组

```Rust
let a: [i32; 4] = [1, 2, 3, 4];
let b = [3; 5]; // => [3, 3, 3, 3, 3]
```

###### 访问数组元素

```Rust
let a = [1, 2, 3, 4, 5];
let first = a[0];
```

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

### 并发

并发编程（concurrent programming），代表程序的不同部分相互独立的执行，而并行编程（parallel programming）代表程序的不同部分于同时执行，这两个概念随着计算机越来越多地利用多处理器的优势显得越来越重要。

Rust 标准库中只提供了 1:1 的线程模型实现，这是因为 Rust 是较为底层的语言。

#### 创建新线程

```Rust
use std::thread;
use std::time::Duration;

fn main() {
    thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }
}
```

注意这个函数的编写方式，当主线程结束时，新的线程也会结束，而不管其是否执行完毕。为了使新线程可以执行完毕，可以使用 `join` 来等待它执行完毕再退成主进程。

```Rust
use std::thread;
use std::time::Duration;

fn main() {
    let handle = thread::spawn(|| {
        for i in 1..10 {
            println!("hi number {} from the spawned thread!", i);
            thread::sleep(Duration::from_millis(1));
        }
    });

    for i in 1..5 {
        println!("hi number {} from the main thread!", i);
        thread::sleep(Duration::from_millis(1));
    }

    handle.join().unwrap();
}
```

#### 线程与 move 闭包

当我们需要在一个线程中使用另一个线程中的数据时，我们可以使用 **move** 闭包。

我们先来看下面的代码

```Rust
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(|| {
        println!("Here's a vector: {:?}", v);
    });

    handle.join().unwrap();
}
```

上面这段代码无法正确运行，因为在新线程中访问了主线程中的变量 `v`。只需要在闭包前加上`move`，这段代码就可以正确运行。

```Rust
use std::thread;

fn main() {
    let v = vec![1, 2, 3];

    let handle = thread::spawn(move || {
        println!("Here's a vector: {:?}", v);
    });

    handle.join().unwrap();
}
```

#### 在线程间传递数据

一个日益流行的确保安全并发的方式是**消息传递**（*message passing*），这里线程或 actor 通过发送包含数据的消息来相互沟通。这个思想来源于 [Go 编程语言文档中](https://golang.org/doc/effective_go.html) 的口号：“不要通过共享内存来通讯；而是通过通讯来共享内存。”（“Do not communicate by sharing memory; instead, share memory by communicating.”）

Rust 中一个实现消息传递并发的主要工具是 **通道**（*channel*），Rust 标准库提供了其实现的编程概念。

编程中的通道有两部分组成，一个发送者（transmitter）和一个接收者（receiver）。代码中的一部分调用发送者的方法以及希望发送的数据，另一部分则检查接收端收到的消息。当发送者或接收者任一被丢弃时可以认为通道被 **关闭**（*closed*）了。

我来看下面的代码

```Rust
use std::thread;
use std::sync::mpsc;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();
    });
    
    let received = rx.recv().unwrap();
    println!("Got: {}", received);
}
```

上面这段代码，在新线程中通过通道向主线程中发送了一个简单的 **hi**.

`mpsc` 是 multiple producer, single consumer 的缩写。Rust 标准库实现通道的方式意味着一个通道可以有多个产生值的 **发送**（*sending*）端，但只能有一个消费这些值的 **接收**（*receiving*）端。

`mpsc::channel` 函数返回一个元组：第一个元素是发送端，而第二个元素是接收端。由于历史原因，`tx` 和 `rx` 通常作为 **发送者**（*transmitter*）和 **接收者**（*receiver*）的缩写，所以这就是我们将用来绑定这两端变量的名字。这里使用了一个 `let` 语句和模式来解构了此元组。

#### 通道与所有权转移

在一个线程中，如果一个值被通过通道发送到了另一个线程，这个值将不再被允许使用。下面的这段代码是无法正确运行的， `val` 已经被发送到了另外的线程，它无法被打印。

```Rust
use std::thread;
use std::sync::mpsc;

fn main() {
    let (tx ,rx) = mpsc::channel();
    
    thread::spawn(move || {
        let val = String::from("hi");
        tx.send(val).unwrap();
        println!("val is {}", val);
    });
    
    let received = rx.recv().unwrap();
    println!("Got: {}", received);
}
```

#### 接收者作为迭代器

先看下面的代码

```Rust
use std::thread;
use std::sync::mpsc;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel();

    thread::spawn(move || {
        let vals = vec![
            String::from("hi"),
            String::from("from"),
            String::from("the"),
            String::from("thread"),
        ];

        for val in vals {
            tx.send(val).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    for received in rx {
        println!("Got: {}", received);
    }
}
```

上面的代码中，在新建的线程中我们发送了一系列字符串到主线程，在主线程中，我们则不是显式调用 `recv` 方法来接受发送过来的字符串，而是把 `rx` 看做是 一个迭代器。当通道被关闭时，迭代器也将结束。

#### 创建多个生产者

之前我们提到了`mpsc`是 *multiple producer, single consumer* 的缩写。那么如何实现 *multiple producer* 呢，答案是通过克隆通道发送端来实现。参考下面的代码

```Rust
use std::thread;
use std::sync::mpsc;
use std::time::Duration;

fn main() {
    let (tx, rx) = mpsc::channel();
    let tx1 = tx.clone();

    thread::spawn(move || {
        let vals = vec![
            String::from("hi"),
            String::from("from"),
            String::from("the"),
            String::from("thread"),
        ];

        for val in vals {
            tx.send(val).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    thread::spawn(move || {
        let vals = vec![
            String::from("HI"),
            String::from("FROM"),
            String::from("THE"),
            String::from("THREAD"),
        ];

        for val in vals {
            tx1.send(val).unwrap();
            thread::sleep(Duration::from_secs(1));
        }
    });

    for received in rx {
        println!("Got: {}", received);
    }
}
```

#### 共享状态并发

虽然消息传递是一个很好的处理并发的方式，但并不是唯一一个。

在某种程度上，任何编程语言中的通道都类似于单所有权，因为一旦将一个值传送到通道中，将无法再使用这个值。共享内存类似于多所有权：多个线程可以同时访问相同的内存位置。前面的章节介绍了只能指针如何使得多所有权成为可能，然而这会增加额外的复杂性，因此需要一某种方式管理不同的所有者。Rust 的类型系统和所有权规则极大的协助了正确地管理这些所有权。作为一个例子，让我们看看互斥器，一个更为常见的共享内存并发原语。

#### 互斥器

**互斥器（mutex）**是 *mutual exclusion* 的缩写，它表示在任意时刻，其只允许一个线程访问某些数据。为了访问互斥器中的数据，线程首先需要通过获取互斥器的 **锁**（*lock*）来表明其希望访问数据。锁是一个作为互斥器一部分的数据结构，它记录谁有数据的排他访问权。因此，我们描述互斥器为通过锁系统 **保护**（*guarding*）其数据。

#### Mutex\<T\> 的 API

我们先来看一个单线程中的例子

```Rust
use std::sync::Mutex;

fn main() {
    let m = Mutext::new();
    
    {
        let mut num = m.lock().unwrap();
        *mut = 6;
    }
    
    println!("m = {:?}", m);
}
```

一旦获取了锁，就可以将返回值（这里是 `num`）视为其内部数据的可变引用了。 类型系统确保了我们在使用 `m` 中的值之前获取锁： `Mutex<i32>` 并不是一个 `i32`，所以必须获取锁才能使用这个 `i32` 的值。

其实，`Mutex<T>` 是一个智能指针。更准确的说，`lock` 调用 **返回** 一个叫做 `MutexGuard` 的智能指针。这个智能指针实现了 `Deref` 来指向其内部数据；其也提供了一个 `Drop` 实现当 `MutexGuard` 离开作用域时自动释放锁。为此，我们不会冒忘记释放锁并阻塞互斥器为其它线程所用的风险，因为锁的释放是自动发生的。

#### 在线程间共享 Mutex\<T\>







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

​	
