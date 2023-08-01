通过 [Rng::gen_range](https://docs.rs/rand/0.8.5/rand/trait.Rng.html#method.gen_range) 生成落在半开区间 \[0, 10) 内的随机数。

```Rust
use rand::Rng;

fn main() {
    let mut rng = rand::thread_rng();
    println!("Integer: {}", rng.gen_range(0..10));
    println!("Float: {}", rng.gen_range(0.0..10.0));
}
```

通过 [Uniform](https://docs.rs/rand/0.8.5/rand/distributions/uniform/struct.Uniform.html) 获取均匀分布的值，它和  [Rng::gen_range](https://docs.rs/rand/0.8.5/rand/trait.Rng.html#method.gen_range) 具有相同的效果， 但是如果是反复在相同区间生成随机值，则前者的速度更快。

```Rust
use rand::distributions::{Distribution, Uniform};

fn main() {
    let mut rng = rand::thread_rng();
    let die = Uniform::from(1..7);

    loop {
        let throw = die.sample(&mut rng);
        println!("Roll the die: {}", throw);
        if throw == 6 {
            break;
        }
    }
}
```