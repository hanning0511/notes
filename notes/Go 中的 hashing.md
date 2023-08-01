Tags: #Golang #hash

---
# Golang 中的 hashing

## 介绍
散列函数是一种将可变长度的输入映射为固定长度的输出的算法。这个函数的返回值被称为哈希值、摘要或只是哈希值。

它主要用于解决密码学的完整性原则。在发送方和接收方之间的通信过程中，信息可能被改变。散列函数可以确保信息不被修改。

散列函数的重要特点是：

- 固定长度的输出。散列函数接收任何大小的信息（输入），并总是产生相同的输出大小。
- 效率： 它必须在计算上不难执行。
- 确定性：相同的输入将总是产生相同的哈希值。

而要用于密码学，它必须具备以下特性。

- Pre-image resistance：给定一个哈希值，应该很难找到一个源于它的信息。
- Second pre-image resistance：给定一个信息 m，要找到另一个产生与m相同哈希值的信息 n 应该是非常困难的。
- Collision resistance：要找到两个产生相同哈希值的不同信息应该是非常困难的。

哈希函数被用于互联网上的许多应用，如：

- 密码存储：你的密码不是以普通的方式保存，而是以散列方式保存在数据库中。至少好的系统是这样做的。
- 唯一的 ID：由于每个消息都必须产生相同的输出，并且没有其他产生相同输出的消息，你可以使用哈希值来唯一地识别一个文件或消息。例如，[[Git]] 就是用这个来识别每个提交。
- 工作量证明（Proff-of-work）：对于一个用户来说，要执行一个动作或发布一些东西，他们必须证明他们已经执行了一个任务。这种证明是一种保证，即用户花了一些时间生成了一个满足评估者条件的答案。例如，这在区块链中被使用。

一些流行的哈希函数包括 **MD5**、**SHA** 和 **Whirpool**。

## 字符串哈希

我们需要 [crypto](https://golang.org/pkg/crypto/)包来计算哈希值。下面是可用的哈希函数。

```go
const (
    MD4         // import golang.org/x/crypto/md4
    MD5         // import crypto/md5
    SHA1        // import crypto/sha1
    SHA224      // import crypto/sha256
    SHA256      // import crypto/sha256
    SHA384      // import crypto/sha512
    SHA512      // import crypto/sha512
    RIPEMD160   // import golang.org/x/crypto/ripemd160
    SHA3_224    // import golang.org/x/crypto/sha3
    SHA3_256    // import golang.org/x/crypto/sha3
    SHA3_384    // import golang.org/x/crypto/sha3
    SHA3_512    // import golang.org/x/crypto/sha3
    SHA512_224  // import crypto/sha512
    SHA512_256  // import crypto/sha512
    BLAKE2s_256 // import golang.org/x/crypto/blake2s
    BLAKE2b_256 // import golang.org/x/crypto/blake2b
    BLAKE2b_384 // import golang.org/x/crypto/blake2b
    BLAKE2b_512 // import golang.org/x/crypto/blake2b
)
```

为了从一个 `string` 或 `byte slice` 中计算出一个哈希值，我们可以使用我们想要的算法的特定包中的 `Sum` 函数。

```go
package main

import (
	"crypto/md5"
	"crypto/sha1"
	"crypto/sha256"
	"fmt"
)

func main() {
	s := "Foo"

	hmd5 := md5.Sum([]byte(s))
	hsha1 := sha1.Sum([]byte(s))
	hsha2 := sha256.Sum256([]byte(s))

	fmt.Printf("   MD5: %x\n", hmd5)
	fmt.Printf("  SHA1: %x\n", hsha1)
	fmt.Printf("SHA256: %x\n", hsha2)
}
```

输出：

```shell
   MD5: 1356c67d7ad1638d816bfb822dd2c25d
  SHA1: 201a6b3053cc1422d2c3670b62616221d2290929
SHA256: 1cbec737f863e4922cee63cc2ebbfaafcd1cff8b790d8cfd2e6a5d550b648afa
```

## 文件哈希

为了计算一个文件的哈希值，我们需要根据其内容创建哈希值。

- 从 [crypto](https://golang.org/pkg/crypto/) 包中创建一个新的 `hash.Hash` (选择我们要使用的 hash 算法)。
- 读取文件的数据并写入通过 `io.Writer` 写入 hash 对象中。
- 通过 `Sum` 方法计算 hash 值。

```go
package main

import (
	"crypto/sha256"
	"io"
	"log"
	"os"
)

func main() {
	file, err := os.Open("test.txt")
	if err != nil {
		log.Fatal(err)
	}
	defer file.Close()

	buf := make([]byte, 30*1024)
	sha256 := sha256.New()
	for {
		n, err := file.Read(buf)
		if n > 0 {
			_, err := sha256.Write(buf[:n])
			if err != nil {
				log.Fatal(err)
			}
		}

		if err == io.EOF {
			break
		}

		if err != nil {
			log.Printf("Read %d bytes: %v", n, err)
			break
		}
	}

	sum := sha256.Sum(nil)
	log.Printf("%x\n", sum)
}
```

或者

```go
package main

import (
        "crypto/sha256"
        "io/ioutil"
        "log"
)

func main() {
        sha256 := sha256.New()
        b, _ := ioutil.ReadFile("test.txt")

        sha256.Write(b)
        
        sum := sha256.Sum(nil)
        log.Printf("%x\n", sum)
}
```