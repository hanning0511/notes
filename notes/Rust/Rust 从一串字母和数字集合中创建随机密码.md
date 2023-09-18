通过 [Alphanumber](https://docs.rs/rand/0.8.5/rand/distributions/struct.Alphanumeric.html) 采样，从 `A-Z`, `a-z`, `0-9` 中，随机生成一串给定长度的字符串。

```Rust
use rand::{thread_rng, Rng};
use rand::distributions::Alphanumeric;

fn main() {
    let rand_string: String = thread_rng()
        .sample_iter(&Alphanumeric)
        .take(30)
        .map(char::from)
        .collect();

    println!("{}", rand_string);
}
```