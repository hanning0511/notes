Tags: #Golang #格式化字符串 
Refs: [String formatting in GoLang](https://golangdocs.com/string-formatting-in-golang),  [Go by Example: String Formatting](https://gobyexample.com/string-formatting)

# Golang 格式化字符串

## Golang 中格式化字符串主要有两种方式

1. 使用 `Printf()` 来打印格式化字符串。
2. 使用 `Sprintf()` 来创建并返回一个字符串，而不是直接打印到控制台。

上面两个方法都是由 `fmt` 包提供的。

### 1. 使用 `Printf()` 格式化字符串

#### 1.1 格式化为实际值

实际的数值可以用一种简单易行的方式进行格式化。所以，让我们从整数开始。

要打印 **ints**，我们需要使用 `%d` 。

```go
fmt.Printf("%d", 42)     // 42
```

要打印浮点数，可以使用 `%f` 。

```go
fmt.Printf("%f", 12.5)    // 12.5
```

浮点数也可以使用**科学记数法**

```go
fmt.Printf("%e\n", 23000000.45)       // 2.3000000e+07
fmt.Printf("%E\n", 23000000.45)       // 2.3000000E+07
```

浮点数可以按照我们需要进行格式化。我们所需要指定的是浮动的宽度。

```go
fmt.Printf("|%4.2f|\n", 1.23)         // |1.23|
```

布尔值可以使用 `%t` 进行格式化。

```go
fmt.Printf("%t", true)                 // true
```

字符可以使用 `%c` 进行格式化。

```go
fmt.Printf("%c", 'a')                  // a
```

格式化字符串使用 `%s` 。

```go
fmt.Printf("%s", "a string")           // a string

// format the size of string
fmt.Printf("|%8s|", "apple")           // |   apple|
```

#### 1.2 格式化 encoded value

格式化二进制值，使用 `%b` 。

```go
fmt.Printf("%b", 12)                   // 1100
```

格式化十六进制值，使用 `%x` 。

```go
fmt.Printf("%x", 123)                  // 7b
```

#### 1.3 获取值的类型

使用 `%T` 来打印值的类型。

```go
fmt.Printf("%T", 42)                   // int
```

#### 1.4 打印指针的值

指针的值可以使用 `%p` 来打印。

```go
package main

import "fmt"

func main() {
	var p *int
	var q int

	q = 42
	p = &q

	fmt.Printf("%p", p)        // 0x40e020
}
```

#### 1.5 格式化结构体

要打印该结构的值，我们可以使用 `%v` 。

使用 `%+v` 会包括结构的字段名。而 `%#v` 将打印该结构的 **Go-representation**。下面是一个例子。

```go
package main

import "fmt"

type Tree struct {
        name string
}

func main() {
        cherry := Tree{"cherry"}

        fmt.Printf("%v\n", cherry)
        fmt.Printf("%+v\n", cherry)
        fmt.Printf("%#v\n", cherry)
}
```

```shell
{cherry}
{name:cherry}
main.Tree{name:"cherry"}
```

## 使用 `Sprintf()` 来创建格式化字符串

```go
s1 := fmt.Sprintf("%d", 42)       // create the string
fmt.Println(s1)                   // 42
```

