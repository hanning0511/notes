Tags: #Rust #extern #use
Refs: [whats  the difference between use and extern](https://stackoverflow.com/questions/29403920/whats-the-difference-between-use-and-extern), [Path and module system changes](https://doc.rust-lang.org/edition-guide/rust-2018/path-changes.html)

# Rust 中的 extern 和 use

`extern crate` is almost never needed since Rust 2018.
You're now only required to add external dependencies to your `Cargo.toml`.
`use` works the same as before.