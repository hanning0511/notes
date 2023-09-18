通过为自定义数据类型实现 [Distribution](https://docs.rs/rand/0.8.5/rand/distributions/trait.Distribution.html) trait, 可以实现自动生成自定义类型的随机值。不过前提是自定义类型的成员变量类型必须是支持生成随机值的类型。

```Rust
use rand::Rng;
use rand::distributions::{Distribution, Standard};

#[derive(Debug)]
struct Point {
    x: i32,
    y: i32,
}

impl Distribution<Point> for Standard {
    fn sample<R: Rng + ?Sized>(&self, rng: &mut R) -> Point {
        let (rand_x, rand_y) = rng.gen();
        Point {
            x: rand_x,
            y: rand_y,
        }
    }
}

fn main() {
    let mut rng = rand::thread_rng();
    let rand_tuple = rng.gen::<(i32, bool, f64)>();
    let rand_point: Point = rng.gen();
    println!("Random tuple: {:?}", rand_tuple);
    println!("Random Point: {:?}", rand_point);
}
```