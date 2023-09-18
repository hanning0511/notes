通过 [gen_range](https://docs.rs/rand/0.8.5/rand/trait.Rng.html#method.gen_range) ,从用户指定的 `bytestring` 中随机生成指定长度的字符串。

```Rust
use rand::Rng;

fn main() {    
    const CHARSET: &[u8] = b"ABCDEFGHIJKLMNOPQRSTUVWXYZ\
                            abcdefghijklmnopqrstuvwxyz\
                            0123456789)(*&^%$#@!~";
    const PASSWORD_LEN: usize = 30;
    let mut rng = rand::thread_rng();

    let password: String = (0..PASSWORD_LEN)
        .map(|_| {
            let idx = rng.gen_range(0..CHARSET.len());
            CHARSET[idx] as char
        })
        .collect();

    println!("{:?}", password);
}
```