# Go 调用 shell 命令

^5c9b7b

Tags: #Golang #Shell


## 执行 shell 命令

```go
package main

import (
        "log"
        "os/exec"
)

func main() {
        cmd := exec.Command("ls", "-alh")
        err := cmd.Run()
        if err != nil {
                log.Fatalf("run ls -alh failed - %v\n", err)
        }
}
```

```shell
$ go run main.go
```

执行上面的代码，看上去啥都没发生，不过 `ls -alh` 确实被执行了。
如果我们在终端 shell 中执行 `ls -alh`, shell 会把进程的 stdout 和 stderr 拷贝到 console，所以我们可以看到。这里我们是通过 go 代码来执行，默认情况下进程的 stdout 和 stderr 会被丢弃掉。

## 执行 shell 命令并显示其输出

```go
package main

import (
        "log"
        "os"
        "os/exec"
)

func main() {
        cmd := exec.Command("ls", "-alh")
        cmd.Stdout = os.Stdout
        cmd.Stderr = os.Stderr
        err := cmd.Run()
        if err != nil {
                log.Fatalf("run ls -alh failed - %v\n", err)
        }
}
```

```shell
$ go run main.go
total 16K
drwxrwxr-x 2 ninghan ninghan 4.0K 9月  22 11:51 .
drwxrwxr-x 7 ninghan ninghan 4.0K 9月  22 11:41 ..
-rw-rw-r-- 1 ninghan ninghan   22 9月  22 11:41 go.mod
-rw-rw-r-- 1 ninghan ninghan  234 9月  22 11:51 main.go
```

`cmd.Stdout` 和 `cmd.Stdout` 被声明为 `io.Writer`, 所以我们可以把它们设为任何实现了 `Write()` 方法的类型的值，比如 `os.File` 或者 in-memory buffer `bytes.Buffer`。

`io.Reader` 和 `io.Writer` 是非常简单但是强大的抽象。

## 执行 shell 命令并捕获其输出

```go
package main

import (
        "fmt"
        "log"
        "os/exec"
)

func main() {
        cmd := exec.Command("ls", "-alh")
        output, err := cmd.CombinedOutput()
        if err != nil {
                log.Fatalf("run ls -alh failed - %v\n", err)
        }
        fmt.Printf("%s\n", output)
```

```shell
go run main.go
total 16K
drwxrwxr-x 2 ninghan ninghan 4.0K 9月  22 13:05 .
drwxrwxr-x 7 ninghan ninghan 4.0K 9月  22 11:41 ..
-rw-rw-r-- 1 ninghan ninghan   22 9月  22 11:41 go.mod
-rw-rw-r-- 1 ninghan ninghan  234 9月  22 13:05 main.go
```

`CombinedOutput()` 方法执行 shell 命令并返回融合的 stdout 和 stderr。下面是 `CombinedOutput` 的具体实现，并不难理解：

```go
func (c *Cmd) CombinedOutput() ([]byte, error) {
	if c.Stdout != nil {
		return nil, errors.New("exec: Stdout already set")
	}
	if c.Stderr != nil {
		return nil, errors.New("exec: Stderr already set")
	}
	var b bytes.Buffer
	c.Stdout = &b
	c.Stderr = &b
	err := c.Run()
	return b.Bytes(), err
}
```

## 分开捕获 stdout 和 stderr

```go
package main

import (
        "bytes"
        "fmt"
        "log"
        "os/exec"
)

func main() {
        cmd := exec.Command("ls", "-alh")

        var stdout, stderr bytes.Buffer

        cmd.Stdout = &stdout
        cmd.Stderr = &stderr

        err := cmd.Run()
        if err != nil {
                log.Fatalf("run ls -alh failed - %v\n", err)
        }

        fmt.Printf("Stdout:\n%s\n", stdout.Bytes())
        fmt.Printf("Stderr:\n%s\n", stderr.Bytes())
}
```

```shell
Stdout:
total 16K
drwxrwxr-x 2 ninghan ninghan 4.0K 9月  22 13:29 .
drwxrwxr-x 7 ninghan ninghan 4.0K 9月  22 11:41 ..
-rw-rw-r-- 1 ninghan ninghan   22 9月  22 11:41 go.mod
-rw-rw-r-- 1 ninghan ninghan  367 9月  22 13:29 main.go

Stderr:

```

## 捕获输出并显示其过程 #1

如果命令需要很长时间才能完成怎么办？
除了捕获 stdout/stderr 之外，如果能在 console 中看到它的过程就更好了。
这有点复杂，但不是很复杂。
首先，创建一个辅助函数，从 reader 复制到 writer，同时捕捉复制的数据。

```go
func copyAndCapture(w io.Writer, r io.Reader) ([]byte, error) {
    var out []byte
    buf := make([]byte, 1024, 1024)
    for {
        n, err := r.Read(buf[:])
        if n > 0 {
            d := buf[:n]
            out = append(out, d...)
            _, err := w.Write(d)
            if err != nil {
                return out, err
            }
        }
        if err != nil {
            // Read returns io.EOF at the end of file, which is not an error for us
            if err == io.EOF {
                err = nil
            }
            return out, err
        }
    }
}
```

处理来自 `Read` 的错误是很微妙的。一个错误 `io.EOF` 意味着我们已经读完了所有东西。这不是一个真正的错误，所以我们把 `io.EOF` 变成 `nil`。

```go
func main() {
  cmd := exec.Command("ls", "-lah")

	var stdout, stderr []byte
	var errStdout, errStderr error
	stdoutIn, _ := cmd.StdoutPipe()
	stderrIn, _ := cmd.StderrPipe()
	err := cmd.Start()
	if err != nil {
		log.Fatalf("cmd.Start() failed with '%s'\n", err)
	}

	// cmd.Wait() should be called only after we finish reading
	// from stdoutIn and stderrIn.
	// wg ensures that we finish
	var wg sync.WaitGroup
	wg.Add(1)
	go func() {
		stdout, errStdout = copyAndCapture(os.Stdout, stdoutIn)
		wg.Done()
	}()

	stderr, errStderr = copyAndCapture(os.Stderr, stderrIn)

	wg.Wait()

	err = cmd.Wait()
	if err != nil {
		log.Fatalf("cmd.Run() failed with %s\n", err)
	}
	if errStdout != nil || errStderr != nil {
		log.Fatal("failed to capture stdout or stderr\n")
	}
	outStr, errStr := string(stdout), string(stderr)
	fmt.Printf("\nout:\n%s\nerr:\n%s\n", outStr, errStr)
}
```

我们有两个输出要复制。为了避免将它们序列化，我们将在一个 [[goroutine]] 中读取一个。
正如 `StdoutPipe` 的文档所说的，`Wait` 将在进程结束时关闭管道。如果我们还没有完成读取，这可能会导致丢失一些输出。
为了防止这种情况，我们使用 `sync.WaitGroup` 来确保处理 `os.Stdout` 的 [[goroutine]] 在我们调用`cmd.Wait` 之前完成读取。

## 捕获输出并显示其过程 #2

前一个方案可以工作，但是 `copyAndCapture` 看上去是重新实现了 `io.Copy`。使用 Go 的接口技术，我们可以复用 `io.Copy`。
我们将编写实现 `io.Writer` 接口的 `CapturingPassThroughWriter` 结构。它将捕获所有写入它的内容，并将其写入底层 `io.Writer`。

```go
// CapturingPassThroughWriter is a writer that remembers
// data written to it and passes it to w
type CapturingPassThroughWriter struct {
    buf bytes.Buffer
    w io.Writer
}

// NewCapturingPassThroughWriter creates new CapturingPassThroughWriter
func NewCapturingPassThroughWriter(w io.Writer) *CapturingPassThroughWriter {
    return &CapturingPassThroughWriter{
        w: w,
    }
}

func (w *CapturingPassThroughWriter) Write(d []byte) (int, error) {
    w.buf.Write(d)
    return w.w.Write(d)
}

// Bytes returns bytes written to the writer
func (w *CapturingPassThroughWriter) Bytes() []byte {
    return w.buf.Bytes()
}

func main() {
    cmd := exec.Command("ls", "-lah")

		var errStdout, errStderr error
		stdoutIn, _ := cmd.StdoutPipe()
		stderrIn, _ := cmd.StderrPipe()
		stdout := NewCapturingPassThroughWriter(os.Stdout)
		stderr := NewCapturingPassThroughWriter(os.Stderr)
		err := cmd.Start()
		if err != nil {
			log.Fatalf("cmd.Start() failed with '%s'\n", err)
		}
	
		var wg sync.WaitGroup
		wg.Add(1)
	
		go func() {
			_, errStdout = io.Copy(stdout, stdoutIn)
			wg.Done()
		}()
	
		_, errStderr = io.Copy(stderr, stderrIn)
		wg.Wait()
	
    err = cmd.Wait()
    if err != nil {
        log.Fatalf("cmd.Run() failed with %s\n", err)
    }
    if errStdout != nil || errStderr != nil {
        log.Fatalf("failed to capture stdout or stderr\n")
    }
    outStr, errStr := string(stdout.Bytes()), string(stderr.Bytes())
    fmt.Printf("\nout:\n%s\nerr:\n%s\n", outStr, errStr)
}
```

## 捕获输出并显示其过程 #3

原来 Go 的标准库实现了 `io.MultiWriter`，它是 `CapturingPassThroughWriter` 的一个更通用的版本。让我们用它来代替。

```go
func main() {
	cmd := exec.Command("ls", "-lah")
	if runtime.GOOS == "windows" {
		cmd = exec.Command("tasklist")
	}

	var stdoutBuf, stderrBuf bytes.Buffer
	cmd.Stdout = io.MultiWriter(os.Stdout, &stdoutBuf)
	cmd.Stderr = io.MultiWriter(os.Stderr, &stderrBuf)

	err := cmd.Run()
	if err != nil {
		log.Fatalf("cmd.Run() failed with %s\n", err)
	}
	outStr, errStr := string(stdoutBuf.Bytes()), string(stderrBuf.Bytes())
	fmt.Printf("\nout:\n%s\nerr:\n%s\n", outStr, errStr)
}
```

## 写入程序的标准输入
 
我们知道如何读取程序的 stdout，但我们也可以写到它的 stdin。

没有 Go 库可以进行 bzip2 压缩（标准库中只有解压缩）。

我们可以使用 `bzip2` 来进行压缩：
- 将数据写入一个临时文件。
- 调用 `bzip2 -c ${file_in}` 并捕获其 stdout

如果我们不需要创建一个临时文件，那就更好了。

大多数压缩程序在 stdin 上接受要压缩/解压的数据。

要在命令行上做到这一点，我们可以使用以下命令：`bzip2 -c ${file_in} >${file_out}`
下面是 Go 语言的实现。

```go
// compress data using bzip2 without creating temporary files
func bzipCompress(d []byte) ([]byte, error) {
    var out bytes.Buffer
    // -c : compress
    // -9 : select the highest level of compresion
    cmd := exec.Command("bzip2", "-c", "-9")
    cmd.Stdin = bytes.NewBuffer(d)
    cmd.Stdout = &out
    err := cmd.Run()
    if err != nil {
        return nil, err
    }
    return out.Bytes(), nil
}
```

## 改变进程的环境变量

关于环境变量，要了解一下两点：

- `os.Environ()` 返回 `[]string`, 里面是环境变量列表，每一个的环境变量的格式都是 `FOO=bar`, 其中 `FOO` 是变量名，`bar` 是变量的值。
- `os.Getenv("FOO")` 返回环境变量 `FOO` 的值。

有时你需要修改被执行程序的环境。

Go 通过设置 `exec.Cmd` 的 `Env` 成员来支持这一点。`cmd.Env` 的格式与 `os.Environ()` 相同。

如果没有设置 `Env`，进程将继承调用进程的环境。

通常，你不想从头开始构建一个全新的环境，而是传递当前进程的一个环境的修改版本。下面是如何添加一个新的变量。

```go
package main

import (
        "fmt"
        "log"
        "os"
        "os/exec"
)

func main() {
        cmd := exec.Command("ls")

        additionalEnv := "FOO=bar"
        newEnv := append(os.Environ(), additionalEnv)
        cmd.Env = newEnv

        out, err := cmd.CombinedOutput()
        if err != nil {
                log.Fatalf("cmd.Run() failed with %s\n", err)
        }
        fmt.Printf("%s", out)
}
```

如果你想删除一个环境或者确保你没有设置同一个变量两次，事情就会变得更加复杂。包 [shurcooL/go/osutil](https://pkg.go.dev/github.com/shurcooL/go/osutil#Environ) 提供了一种更简单的操作环境变量的方法。

## 提前检查可执行文件是否存在

想象一下，你写了一个需要很长时间才能运行的程序。在最后，你调用可执行程序 foo 来执行一项重要任务。

如果可执行文件 foo 不存在，调用将失败。

最好的办法是在一开始就检测到可执行文件 foo 的缺失，并通过描述性的错误信息提前失败。
你可以用 `exec.LookPath` 来做。

```go
package main

import (
        "fmt"
        "log"
        "os/exec"
)

func checkExecutableExists(program string) error {
        path, err := exec.LookPath(program)

        if err != nil {
                return fmt.Errorf("didn't find '%s' executable", program)
        }

        fmt.Printf("'%s' executable is in '%s'\n", program, path)
        return nil
}

func main() {
        if err := checkExecutableExists("ls"); err != nil {
                panic(err)
        }

        cmd := exec.Command("ls")
        err := cmd.Run()
        if err != nil {
                log.Fatalf("cmd.Run() failed with %s\n", err)
        }
}
```