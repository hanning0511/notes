使用通过 [rand::thread_rng](https://docs.rs/rand/0.8.5/rand/fn.thread_rng.html) 获得的随机数发生器 [rand::Rng](https://docs.rs/rand/0.8.5/rand/trait.Rng.html) 生成随机数。每个线程都有一个预初始化的发生器。整数在类型的范围内是均匀分布的，浮点数从 0 到不包括 1 的范围内均匀分布。

```Rust
use rand::Rng;

fn main() {
    let mut rng = rand::thread_rng();

    let n1: u8 = rng.gen();
    let n2: u16 = rng.gen();
    println!("Random u8: {}", n1);
    println!("Random u16: {}", n2);
    println!("Random u32: {}", rng.gen::<u32>());
    println!("Random i32: {}", rng.gen::<i32>());
    println!("Random float: {}", rng.gen::<f64>());
}

```