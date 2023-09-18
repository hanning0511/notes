Tags: #Rust #iter 
Refs: https://www.becomebetterprogrammer.com/rust-iter-vs-iter_mut-vs-into_iter/

简单来说：
1. `iter()` 迭代的是不可变引用 `&T`。
2. `iter_mut()` 迭代的是可变引用 `&mut T`。
3. `into_iter()` 迭代的转移所有权的值（moved value），可变或不可变取要看具体情况。

`iter()` 最常用，下面是 `iter()` 的一个例子：

```rust
let fruits = vec!["Banana", "Apple", "Grapes", "Pineapple"];
println!(
	"{}",
	fruits
		.iter()
		.map(|fruit_name: &&str| fruit_name.len())
		.fold(0, |accumulated, fruit_name_length| {
			accumulated + fruit_name_length
		})
); // Should print 26
```
`iter_mut()` 用的不多，它迭代的是每个元素的可变引用， 可以通过下面的例子来理解其用法：

```rust
let mut numbers = vec![1, 2, 3];
numbers.iter_mut().for_each(|n| *n += 1)；
println!("{:?}", numbers); // should print [2, 3, 4]
```

上面这段代码和下面的代码时等价的：

```rust
for n in numbers.iter_mut() {
	*n += 1;
}
```

`into_iter()` 的名字来自于 trait `IntoIterator`，所以理解 `IntoIterator` trait 对理解 `into_iter()` 会有很帮助。如果开发者想要让某个数据类型支持转换为迭代器，那么就需要为这个数据类型实现 `IntoIterator` trait。这对于表示某种数据集合的数据类型非常有用，因为它使得通过 `for` 循环的语法来迭代数据类型所表示的集合。 