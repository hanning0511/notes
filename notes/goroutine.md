Tags: #Golang #Goroutines

---

# Goroutines

## 什么是 goroutine

Goroutines 是与其他函数或方法同时运行的函数或方法。Goroutines 可以被认为是轻量级的线程。与线程相比，创建一个 Goroutine 的成本很小。因此，在 Go 应用程序中，通常会有成千上万的 Goroutine 同时运行。

## 相对于线程，Goroutines 的优势

- 与线程相比，Goroutines 非常便宜。它们的堆栈大小只有几 kb，而且堆栈可以根据应用程序的需要进行增长和收缩，而在线程的情况下，堆栈大小必须被指定，而且是固定的。
- Goroutines 被复用到数量较少的操作系统线程中。在一个有数千个 Goroutine 的程序中，可能只有一个线程。如果该线程中的任何一个 Goroutine 阻塞了，例如等待用户输入，那么就会创建另一个操作系统线程，其余的 Goroutine 被转移到新的操作系统线程。所有这些都是由运行时处理的，而我们作为程序员被从这些复杂的细节中抽象出来，并被赋予一个干净的 API 来处理并发问题。
- Goroutines 使用 [[channel]] 进行通信。[[channel]] 的设计可以防止在使用 Goroutines 访问共享内存时发生竞态。[[channel]] 可以被认为是一个管道，Goroutines 通过它进行通信。

## 如何创建一个 Goroutine

在函数或方法的调用前加上关键字 `go`，你就会有一个新的 Goroutine 同时运行。

```go
package main

import (  
    "fmt"
)

func hello() {  
    fmt.Println("Hello world goroutine")
}
func main() {  
    go hello()
    fmt.Println("main function")
}
```

在第 11 行，`go hello()` 启动了一个新的 Goroutine。现在 `hello()` 函数将与 `main()` 函数同时运行。主函数在它自己的 Goroutine 中运行，它被称为主 Goroutine。

这个程序只输出主函数的文本。我们启动的 Goroutine 发生了什么？我们需要了解 Goroutines 的两个主要属性，以理解为什么会发生这种情况。

- 当一个新的 Goroutine 被启动时，Goroutine 的调用会立即返回。与函数不同，不会等待 Goroutine 完成执行。立即返回到 Goroutine 调用后的下一行代码，Goroutine 的任何返回值都被忽略。
- 主 Goroutine 应该正在运行，以便任何其他 Goroutine 能够运行。**如果主 Goroutine 终止，那么程序将被终止，其他 Goroutine 将不会运行**。

现在能理解为什么我们的 Goroutine 没有运行了。在第 11 行调用 `hello()` 后，立即返回到下一行代码，没有等待 hello goroutine 完成并打印 *main function*。然后主 Goroutine 终止了，因为没有其他代码可以执行，因此 hello Goroutine 没有机会运行。

我们可以修复这个问题

```go
package main

import (  
    "fmt"
    "time"
)

func hello() {  
    fmt.Println("Hello world goroutine")
}
func main() {  
    go hello()
    time.Sleep(1 * time.Second)
    fmt.Println("main function")
}
```

在上述程序的第 13 行，我们调用了时间包的 `Sleep` 方法，使正在执行的 go routine 休眠。在这种情况下，主 Goroutine 被睡眠 1 秒。现在，在主 Goroutine 终止之前，对 `go hello()` 的调用有足够的时间来执行。这个程序首先打印出 *Hello world goroutine*，等待 1 秒钟，然后打印出 *main function*。

这种在主 Goroutine 中使用睡眠来等待其他 Goroutine 完成它们的执行的方式是我们用来理解 Goroutine 如何工作的一个方法。[[channel]] 可以用来阻塞主 Goroutine，直到所有其他 Goroutine 完成它们的执行。

## 创建多个 Goroutinues

```go
package main

import (  
    "fmt"
    "time"
)

func numbers() {  
    for i := 1; i <= 5; i++ {
        time.Sleep(250 * time.Millisecond)
        fmt.Printf("%d ", i)
    }
}
func alphabets() {  
    for i := 'a'; i <= 'e'; i++ {
        time.Sleep(400 * time.Millisecond)
        fmt.Printf("%c ", i)
    }
}
func main() {  
    go numbers()
    go alphabets()
    time.Sleep(3000 * time.Millisecond)
    fmt.Println("main terminated")
}
```

上述程序在第 21 和 22 行启动了两个 Goroutine。这两个 Goroutine 现在同时运行。**numbers Goroutine** 最初休眠 250 毫秒，然后打印 1，然后再次休眠并打印 2，同样的循环发生直到打印 5。同样地，**alphabets Goroutine** 打印从 a 到 e 的字母，有 400 毫秒的休眠时间。**main Goroutine** 启动了 numbers 和 alphabets Goroutine，休眠时间为 3000 毫秒，然后终止。

这个程序输出：

```shell
1 a 2 3 b 4 c 5 d e main terminated  
```

下面的图片描述了这个程序是如何工作的。

![[how-goroutine-works.png]]

图片的第一部分用蓝色表示 **number Goroutine**，第二部分用栗色表示 **alphabets Goroutine**，第三部分用绿色表示 **main Goroutine**，最后一部分用黑色表示合并了上述三个部分，向我们展示了程序的工作原理。每个方框顶部的 0 毫秒、250 毫秒等字符串代表了以毫秒为单位的时间，每个方框底部的输出表示为1、2、3，以此类推。蓝色方框告诉我们，250 毫秒后打印 1，500 毫秒后打印 2，以此类推。最后一个黑框的底部有数值 `1 a 2 3 b 4 c 5 d e main terminated`，这也是程序的输出。这个图像很直观，能够帮助理解这个程序是如何工作的。