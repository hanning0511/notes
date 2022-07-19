
# Rust

## 基础知识

[[变量和常量]]
[[数据类型]]
[[常用集合类型]]
[[结构体]]
[[多线程]]
[[迭代器]]

[[从标准输入中读入信息]]
[[命令行参数解析]]
[[文件读写]]
[[序列化]]

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
