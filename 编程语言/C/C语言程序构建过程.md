# C 语言程序构建过程

```mermaid
graph LR
预处理/preprocess --> 汇编/assemble --> 编译/compile --> 链接/link
```

## 预处理程序

预处理是 C 语言构建过程的第一阶段，在这个阶段，所有的预处理器指令都被评估。

![Preprocessor stage](https://media-exp1.licdn.com/dms/image/C5612AQFyMmY6NBa4uQ/article-inline_image-shrink_1000_1488/0/1567691843418?e=1661385600&v=beta&t=AhZlgC7aMyfcQIe9t8Oa3NQQG54GngekJr2CVqRe2Ys)

- 输入是 `.c` 文件。
- 输出是 `.i` 文件。
- 预处理器将输入的 C 文件中的注释剥离出来。通过对以 `#` 开头的行进行替换来评估预处理器指令，然后产生一个没有任何预处理器指令的纯 C 代码。
- 请注意，如果在预处理阶段发生错误，你通常不会知道它的位置，因为预处理的输出直接进入编译器，错误可能发生在你使用预处理指令的那几行。

## 编译器

在这个阶段，C 语言代码被编译器转换为特定架构的汇编指令；这种转换并不是一对一的行的映射，而是将 C 语言操作分解为许多汇编操作。每个操作本身是一个非常基本的任务。![No alt text provided for this image](https://media-exp1.licdn.com/dms/image/C4D12AQFYd7gGrtqesA/article-inline_image-shrink_1000_1488/0/1567692116929?e=1661385600&v=beta&t=WFHOp7a0gvEzKaz3ygQ62BqxR26PS91F86PVKp3BPj4)

- 输入是 `.i` 文件。
- 输出是 `.s` 或者 `.asm` 文件。

为了了解这个阶段到底发生了什么，我们必须知道编译器是如何工作的，它的组成部分是什么。

![No alt text provided for this image](https://media-exp1.licdn.com/dms/image/C4E12AQEBj9RxyOwUIA/article-inline_image-shrink_1000_1488/0/1567692307166?e=1661385600&v=beta&t=JFD9q8RpCiAs-bCgBatHubzTWqwQvfPj-KkGA9GtXp0)

编译器的第一部分被称为 "前端"（Front End）：在这个部分，这部分做的是对程序语法和语义的分析。

![No alt text provided for this image](https://media-exp1.licdn.com/dms/image/C4E12AQEvELBNHXSMjA/article-inline_image-shrink_1500_2232/0/1567692482166?e=1661385600&v=beta&t=yjQmxBKDHUCB66-5ROhD4Lo7LM69rYWRNNeaxKCF13E)

- 编译器前端部分的第一阶段是扫描输入文本，通过识别关键字、标识符、运算符和字面意义等标记进行Tokenization，然后将扫描的标记传递给解析工具，确保标记按照 C 规则组织，以避免编译器语法错误。
- 编译器前端的第二阶段是检查已被解析的句子是否有正确的含义。而且，这种语义检查，如果失败了，就会得到一个语义错误。
- 在语义分析中，真正发生的关键事情之一是与程序中存在的所有变量有关。为此，编译器在一个叫做符号表（symbol table）的结构中维护所有声明的变量的信息。一旦变量被锁定，它就会得到其属性；这些属性包括其类型、范围等等。
- 当发现该语句在语义上是有意义的，并且是正确的，编译器就会进行下一个动作，即把这个被看到的句子翻译成内部表示，称为中间表示；这里的想法是把高级语言中的结构，不管是什么语言，转换成更接近汇编的形式；以便能够在不同的目标上编译不同的语言。

**语义错误的类型**：

- 未声明的变量，在没有声明的情况下被使用。
- 在一个给定的范围内无法使用的变量，尽管它们被声明了。
- 不兼容的类型，例如，如果你检查了一个变量名，而这个变量名恰好是一个字符，那么这个特定名称的用法就不应该是加法语句的一部分。因为把一个字符加到别的东西上是没有意义的。

**符号表**：

符号表向我们展示了程序中声明的不同变量，以及在当前范围内可用的变量。

例如，如果你遇到一个声明性的语句。

![No alt text provided for this image](https://media-exp2.licdn.com/dms/image/C4E12AQE2cFuH84risQ/article-inline_image-shrink_1500_2232/0/1567693014100?e=1661385600&v=beta&t=IAGY70RJceuwpqbbsEqf2o0D7F4BWRkYH-NByIpWvuA)

编译器的第二部分被称为 "后端"（Back End）：在这个部分做的是优化和代码生成。

- 编译器后端部分的第一阶段是优化；如今，编译器已经足够聪明，能够不仅仅是编译代码，而且还能提供一些优化。
- 优化的形式有很多，如将代码转化为更小或更快但功能等价的代码，函数的内联扩展，去除无用代码，循环解卷，以及寄存器分配。
- 编译器后端部分的最后阶段是代码生成；在这个阶段，编译器将优化的中间代码结构转换为汇编代码。

## 汇编器

在这个阶段，由编译器生成的汇编代码被汇编器转换为目标代码。

- 输入是  `.s` 或  `.asm` 文件。
- 输出是 `.o` 或 `.obj` 文件。
- 请注意，现在的编译器可以生成目标代码，而不需要独立的汇编器。
- 这个阶段的输出是一个包含操作代码和数据部分的对象文件。

代码生成完成后，编译器为代码和数据分段分配内存；每个部分都有不同的信息，并由名称或存储在其中的信息的属性来定义。

**不同的内存段**：

- .text 段

  - **.text** 段，也被称为代码段或简称为文本，是对象文件中或内存中的程序部分之一，其中包含可执行指令。
  - 作为一个内存区域，**.text** 段可以被置于堆或栈的下面，以防止堆和栈溢出覆盖它。

- .data 段

  初始化数据段，通常简单地称为数据段。数据段是程序的虚拟地址空间的一部分，它包含了由程序员初始化的全局变量和静态变量。

- .bass 段

  未初始化的数据从数据段的末尾开始，包含所有全局变量和静态变量，这些变量被初始化为零或在源代码中没有明确初始化。

- stack 段

  堆栈数据段存储临时数据，如局部变量和返回地址。

- heap 段

  动态数据存储。

汇编器的最终输出是一个对象文件，为了更好地理解下一阶段要发生的事情，我们必须知道这个对象文件里面到底是什么。

![No alt text provided for this image](https://media-exp2.licdn.com/dms/image/C4E12AQG5fL84no-_Jg/article-inline_image-shrink_1500_2232/0/1567693804695?e=1661385600&v=beta&t=dQ_ImUz6J4P13Esz-PfHBcpCV7MK2RwNB0RDFjK3_Ao)

- 对象文件包含静态变量的部分；在整个程序中是可用的。
- 符号表用于存储所有的变量名称和它们的属性。
- 调试信息部分，具有原始源代码和调试器所需信息之间的映射关系。
- 导出部分包含全局符号，无论是函数还是变量。
- 导入部分包含需要从其他对象文件中获得的符号名称。
- 导出、导入部分和符号表部分在链接阶段被链接器使用。

## 链接器

![No alt text provided for this image](https://media-exp1.licdn.com/dms/image/C4E12AQE48SgStVLuMA/article-inline_image-shrink_1000_1488/0/1567694279315?e=1661385600&v=beta&t=bs3ovBIMQmFL2W4e1V5hHuUt-Iht1iNu76hFgly8uVY)

在这个阶段，由汇编器生成的不同对象文件被链接器转换成一个可重定位的文件。

- 输入是 `.o` 或 `.obj` 文件，和 C 标准库。
- 输出是可重定位文件。
- 把目标文件结合到一起的过程中，链接器做了下面这些操作：
  1. 符号解析。
  2. 重定位。

**符号解析**

在多文件程序中，如果有任何对另一个文件中定义的标签的引用，汇编器会将这些引用标记为 "未解析"。当这些文件被传递给链接器时，链接器会从其他对象文件中确定这些引用的值，并以正确的值修补代码。而如果链接器没有在任何对象文件中找到对这些标签的引用，它就会抛出一个链接错误 "未解析的对变量的引用"。

如果链接器发现两个对象文件中定义了相同的符号，它将报告一个 "重新定义 "的错误

**重定位**

重新定位是改变已经分配给标签的地址的过程。这也将涉及修补所有引用以反映新分配的地址。

主要是出于以下两个原因进行重新定位。段的合并，以及段的放置。

**段合并**

![No alt text provided for this image](https://media-exp1.licdn.com/dms/image/C4E12AQE6CdVSEF-13g/article-inline_image-shrink_1500_2232/0/1567694358805?e=1661385600&v=beta&t=dh6s8aEAMXqJAjonbxNKe42R6asgHxJHtxN_oS0TXYM)

在每个文件中，链接器负责将输入文件的部分合并到输出文件的部分。默认情况下，每个文件中具有相同名称的部分被连续放置，并对引用的标签进行修补以反映新的地址。

**段放置**

![No alt text provided for this image](https://media-exp2.licdn.com/dms/image/C4E12AQF66ZIOBTnBbA/article-inline_image-shrink_1500_2232/0/1567694623393?e=1661385600&v=beta&t=LApC4A_kFmqerK727HSUFfPqajDcQTuf4HIyfpmIVX4)

![No alt text provided for this image](https://media-exp1.licdn.com/dms/image/C4E12AQEVR6kJSO6NGg/article-inline_image-shrink_1500_2232/0/1567694767710?e=1661385600&v=beta&t=UGYKPfbPqocj_-80RwLmQiDdYVysY4TBxpmmIt7T_x0)

当一个程序被组装起来时，每个部分都被假定从地址0开始。因此，标签被分配了相对于该部分开始的值。当最终的可执行程序被创建时，该部分被放置在某个地址 X 上，所有对该部分定义的标签的引用都被增加 X，以便它们指向新的位置。

## 定位器

![No alt text provided for this image](https://media-exp1.licdn.com/dms/image/C4E12AQG9nR9Zua20fQ/article-inline_image-shrink_1000_1488/0/1567695209559?e=1661385600&v=beta&t=Y6LM-CFXp8fpukUEP2M-MgHewjDTZe7gR7lnXfF1J-0)

在这个阶段，使用定位器对从链接器中产生的可重定位文件分配物理地址的过程被执行。

- 输入是可重定位文件和链接脚本。
- 输出是可执行文件。
- 定位器是一种工具，它执行从可重定位程序到可执行二进制图像的转换。
- 链接器脚本文件向定位器提供所需的实际内存布局信息，然后由定位器进行转换，产生一个可执行的二进制文件。
- 注意，定位器可以作为一个单独的工具也有可能是和链接器在一起。

**链接器接本文件**

链接器脚本文件或链接器配置文件负责告诉定位器如何将可执行文件映射到适当的地址。

- 它控制着输出文件的内存布局，因为它提供了目标文件的内存信息作为定位器的输入。
- LCF 定义了物理内存布局（Flash/SRAM）和不同程序区域的位置。
- LCF 高度依赖于编译器，所以每个人都会有自己的格式。

![No alt text provided for this image](https://media-exp2.licdn.com/dms/image/C4E12AQFzcsQyS3d6fA/article-inline_image-shrink_1500_2232/0/1567695255992?e=1661385600&v=beta&t=2Pmli95ND9hlBo3gXpdjqYwuMYsvl-EQ65c78D8-6Xo)

C 语言构建过程的最终输出将是我们的源代码的可执行二进制文件。