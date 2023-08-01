# Golang 读取 `.gz` 格式文本文件

```go
func ReadGzFile(filename string) ([]byte, error) {
    fi, err := os.Open(filename)
    if err != nil {
        return nil, err
    }
    defer fi.Close()

    fz, err := gzip.NewReader(fi)
    if err != nil {
        return nil, err
    }
    defer fz.Close()

    s, err := ioutil.ReadAll(fz)
    if err != nil {
        return nil, err
    }
    return s, nil   
}
```