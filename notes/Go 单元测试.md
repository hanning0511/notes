Tags: #Golang #单元测试 
Refs: [How to Write Unit Test in Go](https://www.digitalocean.com/community/tutorials/how-to-write-unit-tests-in-go-using-go-test-and-the-testing-package), [Add a Test](https://go.dev/doc/tutorial/add-a-test)

# GoLang 单元测试

Go 对单元测试的内置支持使得编写单元测试非常容易。具体来说，利用命名规则、Go 的 **test package** 和 `go test` 命令，你可以快速编写和执行测试。

按照约定，Go 单元测试代码的文件总是和它们所测试的代码在同一个文件或包中，并且以 `_test.go` 结尾。当执行 `go build` 的时候，这些以 `_test.go` 结尾的文件不会被编译。

第一步，创建一个示例程序

```shell
$ mkdir math
$ cd math
$ touch math.go
```

在 `math.go` 中编写下面的代码

```go
package math

// Add is a function that sums two integers
func Add(x, y int) int {
	return x + y
}
```

创建 `math_test.go`

```shell
$ touch math_test.go
```

在 `math_test.go` 中编写测试代码

```go
package math

import "testing"

func TestAdd(t *testing.T) {
	got := Add(4, 6)
	want := 10

	if got != want {
		t.Errorf("got %q, wanted %q", got, want)
	}
}
```

执行测试

```shell
$ go test
PASS
ok      learn/math      0.003s
```

执行测试，同时输出更多信息

```shell
$ go test -v
=== RUN   TestAdd
--- PASS: TestAdd (0.00s)
PASS
ok      learn/math      0.003s
```

