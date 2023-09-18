Tags: #Rust #UniteTest

## 编写单元测试

```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

// This is a really bad adding function, its purpose is to fail in this
// example.
#[allow(dead_code)]
fn bad_add(a: i32, b: i32) -> i32 {
    a - b
}

#[cfg(test)]
mod tests {
    // Note this useful idiom: importing names from outer (for mod tests) scope.
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(1, 2), 3);
    }

    #[test]
    fn test_bad_add() {
        // This assert would fire and test will fail.
        // Please note, that private functions can be tested too!
        assert_eq!(bad_add(1, 2), 3);
    }
}
```

## 测试 panic

如果你的函数可能在多种情况下发生 panic，使用属性 `#[should_panic]` 检查应该在某些情况下发生 panic 的函数。此属性接受可选参数 `expected =`，其中包含 panic 消息的文本。如果你的函数可能以多种方式发生 panic，则可以确保测试正确的 panic。

```rust
pub fn divide_non_zero_result(a: u32, b: u32) -> u32 {
    if b == 0 {
        panic!("Divide-by-zero error");
    } else if a < b {
        panic!("Divide result is zero");
    }
    a / b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_divide() {
        assert_eq!(divide_non_zero_result(10, 2), 5);
    }

    #[test]
    #[should_panic]
    fn test_any_panic() {
        divide_non_zero_result(1, 0);
    }

    #[test]
    #[should_panic(expected = "Divide result is zero")]
    fn test_specific_panic() {
        divide_non_zero_result(1, 10);
    }
}
```

## 执行特定的单元测试

```shell
$ cargo test test_any_panic
```

## 忽略测试用例

可以使用 `#[ignore]` 属性来把测试用例标记为忽略。被标记为 **ignore** 的测试用例不会被执行。如果需要单独运行被标记为 **ignore** 的测试用例，可以使用 `cargo test -- --ignore` 命令。

```rust
pub fn add(a: i32, b: i32) -> i32 {
    a + b
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_add() {
        assert_eq!(add(2, 2), 4);
    }

    #[test]
    fn test_add_hundred() {
        assert_eq!(add(100, 2), 102);
        assert_eq!(add(2, 100), 102);
    }

    #[test]
    #[ignore]
    fn ignored_test() {
        assert_eq!(add(0, 0), 0);
    }
}
```

## 输出测试用例中的打印信息

默认情况下执行 `cargo test` 时不会显示测试用例代码中的打印信息，如果希望能够看到测试用例中打印出的信息，，可以使用 `cargo test -- --nocapture`。