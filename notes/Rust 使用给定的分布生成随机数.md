通过 [rand](https://crates.io/crates/rand) crate 生成的随机数，默认情况下是平均分布的。[rand_distr](https://crates.io/crates/rand_distr) crate 提供了其他类型的分布算法。想要使用 **rand_distr**， 需要先初始化想要的分布，然后使用 [Distribution::sample](https://docs.rs/rand/0.8.5/rand/distributions/trait.Distribution.html#tymethod.sample) 通过 [rand::Rng](https://docs.rs/rand/0.8.5/rand/trait.Rng.html) 对分布进行采样。

```Rust
use rand_distr::{Distribution, Normal, NormalError};
use rand::thread_rng;

fn main() -> Result<(), NormalError> {
    let mut rng = thread_rng();
    let normal = Normal::new(2.0, 3.0)?;
    let v = normal.sample(&mut rng);
    println!("{} is from a N(2, 9) distribution", v);
    Ok(())
}
```