Tag: #Rust #deserialize

---

通过嵌入 `serde_json::Value` 到 `struct` 可以实现对任意 json 结构进行反序列化。

```Rust
// Deserialize something like `{"id": "x", "body": /* any valid json */}`
#[derive(Serialize, Deserialize)]
struct MyStruct {
    id: String,
    body: serde_json::Value,
}
```

然后，可以直接使用 `body`, 或者也可以使用 `serde_json::from_value` 对 `body` 进一步反序列化。

```Rust
let my_struct: MyStruct = serde_json::from_str(j).unwrap();
if my_struct.id == "x" {
    let x: MyOtherStruct = serde_json::from_value(my_struct.body).unwrap();
    /* ... */
}
```

如果需要对整个 json 结构反序列化：

```Rust
let value: serde_json::Value = serde_json::from_str(j).unwrap();
```