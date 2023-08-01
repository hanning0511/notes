要想在 Rust 中找到数组中某个元素的索引，可以使用函数 `iter()` 遍历数组中的元素，然后使用 `position()` 函数。最后，使用 `unwrap()` 函数来提取包含的值，也就是数组元素的索引。 这个方法同样适用于 Vector。让我们来看看这个代码的实现。

```Rust
let a = [3, 2, 6, 7];
let v = 6;

if let Some(index) = a.iter().position(|&x| x == v) {
	println!("index of {} is {}", v, index);
} else {
	println!("{} is not in the array");
}
```