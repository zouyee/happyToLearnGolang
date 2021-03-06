###					一、Golang小学入门###

##### 1.1 Golang简介
​	Go是Google开发的一种编译型，可并行化，并具有垃圾回收功能的编程语言。
​	罗布·派克（Rob Pike）,罗伯特·格瑞史莫（Robert Griesemer），及肯·汤普逊于2007年9月开始设计Go语言，随后Ian Lance Taylor, Russ Cox加入项目中。Go语言是基于Inferno操作系统所开发的。Go语言于2009年11月正式宣布推出，成为开放源代码项目，并在Linux及Mac OS X平台上进行了实现，后追加Windows系统下的实现。
​	2015年，Golang的受欢迎程度急剧上升，随着一大批PaaS开源工具的兴起与流行，诸如docker、kubernetes等，开源社区的发展促进Golang的流行，Golang的高效等特性同样为开源软件的性能提供保障，两者相辅相成。

###### 					1.1.1 格式化

​	格式化问题总是充满了争议，但却始终没有形成统一的定论。虽说人们可以适应不同的编码风格， 但抛弃这种适应过程岂不更好？若所有人都遵循相同的编码风格，在这类问题上浪费的时间将会更少。 问题就在于如何实现这种设想，而无需冗长的语言风格规范。

​	在Go中我们另辟蹊径，让机器来处理大部分的格式化问题。`gofmt` 程序（也可用 `go fmt`，它以包为处理对象而非源文件）将Go程序按照标准风格缩进、 对齐，保留注释并在需要时重新格式化。若你想知道如何处理一些新的代码布局，请尝试运行`gofmt`；若结果仍不尽人意，请重新组织你的程序（或提交有关 `gofmt` 的Bug），而不必为此纠结。

​	举例来说，你无需花时间将结构体中的字段注释对齐，`gofmt` 将为你代劳。 假如有以下声明：

```go
type T struct {
	name string // 对象名
	value int // 对象值
}
```

`gofmt` 会将它按列对齐为：

```go
type T struct {
	name    string // 对象名
	value   int    // 对象值
}
```

标准包中所有的Go代码都已经用 `gofmt` 格式化过了。

还有一些关于格式化的细节，它们非常简短：

- 缩进

  我们使用制表符（tab）缩进，`gofmt` 默认也使用它。在你认为确实有必要时再使用空格。

- 行的长度

  Go对行的长度没有限制。如果一行实在太长，也可进行折行并插入适当的tab缩进。

- 括号

  比起C和Java，Go所需的括号更少：控制结构（`if`、`for` 和 `switch`）在语法上并不需要圆括号。此外，操作符优先级处理变得更加简洁，因此`x<<8 + y<<16`正表述了空格符所传达的含义。


  	

###### 	1.1.2 注释

​	Go语言支持C风格的块注释 `/* */` 和C++风格的行注释 `//`。 行注释更为常用，而块注释则主要用作包的注释，当然也可在禁用一大段代码时使用。

  	`godoc` 既是一个程序，又是一个Web服务器，它对Go的源码进行处理，并提取包中的文档内容。 出现在顶级声明之前，且与该声明之间没有空行的注释，将与该声明一起被提取出来，作为该条目的说明文档。 这些注释的类型和风格决定了 `godoc` 生成的文档质量。

 	 每个包都应包含一段包注释，即放置在包子句前的一个块注释。对于包含多个文件的包， 包注释只需出现在其中的任一文件中即可。包注释应在整体上对该包进行介绍，并提供包的相关信息。 它将出现在 `godoc` 页面中的最上面，并为紧随其后的内容建立详细的文档。

```go
/*
	regexp 包为正则表达式实现了一个简单的库。

	该库接受的正则表达式语法为：

	正则表达式:
		串联 { '|' 串联 }
	串联:
		{ 闭包 }
	闭包:
		条目 [ '*' | '+' | '?' ]
	条目:
		'^'
		'$'
		'.'
		字符
		'[' [ '^' ] 字符遍历 ']'
		'(' 正则表达式 ')'
*/
package regexp
```

若某个包比较简单，包注释同样可以简洁些。

```
// path 包实现了一些常用的工具，以便于操作用反斜杠分隔的路径.

```

注释无需进行额外的格式化，如用星号来突出等。生成的输出甚至可能无法以等宽字体显示， 因此不要依赖于空格对齐，`godoc` 会像 `gofmt` 那样处理好这一切。 注释是不会被解析的纯文本，因此像HTML或其它类似于 `_这样_` 的东西将按照 **原样** 输出，因此不应使用它们。`godoc` 所做的调整， 就是将已缩进的文本以等宽字体显示，来适应对应的程序片段。 [`fmt` 包](http://golang.org/pkg/fmt/)的注释就用了这种不错的效果。

`godoc` 是否会重新格式化注释取决于上下文，因此必须确保它们看起来清晰易辨： 使用正确的拼写、标点和语句结构以及折叠长行等。

在包中，任何顶级声明前面的注释都将作为该声明的文档注释。 在程序中，每个可导出（首字母大写）的名称都应该有文档注释。

文档注释最好是完整的句子，这样它才能适应各种自动化的展示。 第一句应当以被声明的东西开头，并且是单句的摘要。

```
// Compile 用于解析正则表达式并返回，如果成功，则 Regexp 对象就可用于匹配所针对的文本。
func Compile(str string) (regexp *Regexp, err error) {

```

若注释总是以名称开头，`godoc` 的输出就能通过 `grep` 变得更加有用。假如你记不住“Compile”这个名称，而又在找正则表达式的解析函数， 那就可以运行

```
$ godoc regexp | grep parse

```

若包中的所有文档注释都以“此函数…”开头，`grep` 就无法帮你记住此名称。 但由于每个包的文档注释都以其名称开头，你就能看到这样的内容，它能显示你正在寻找的词语。

```
$ godoc regexp | grep parse
	Compile parses a regular expression and returns, if successful, a Regexp
	parsed. It simplifies safe initialization of global variables holding
	cannot be parsed. It simplifies safe initialization of global variables
$

```

Go的声明语法允许成组声明。单个文档注释应介绍一组相关的常量或变量。 由于是整体声明，这种注释往往较为笼统。

```
// 表达式解析失败后返回错误代码。
var (
	ErrInternal      = errors.New("regexp: internal error")
	ErrUnmatchedLpar = errors.New("regexp: unmatched '('")
	ErrUnmatchedRpar = errors.New("regexp: unmatched ')'")
	...
)

```

即便是对于私有名称，也可通过成组声明来表明各项间的关系，例如某一组由互斥体保护的变量。

```
var (
	countLock   sync.Mutex
	inputCount  uint32
	outputCount uint32
	errorCount  uint32
)
```

###### 	1.1.3 命名

​	正如命名在其它语言中的地位，它在 Go 中同样重要。有时它们甚至会影响语义： 例如，某个名称在包外是否可见，就取决于其首个字符是否为大写字母。 因此有必要花点时间来讨论Go程序中的命名约定。

​	Go 的源文件以 `.go` 为后缀名存储在计算机中，这些文件名均由小写字母组成，如 `scanner.go` 。如果文件名由多个部分组成，则使用下划线 `_` 对它们进行分隔，如 `scanner_test.go` 。文件名不包含空格或其他特殊字符。

​	一个源文件可以包含任意多行的代码，Go 本身没有对源文件的大小进行限制。

​	你会发现在 Go 代码中的几乎所有东西都有一个名称或标识符。另外，Go 语言也是区分大小写的，这与 C 家族中的其它语言相同。有效的标识符必须以字符（可以使用任何 UTF-8 编码的字符或 `_`）开头，然后紧跟着 0 个或多个字符或 Unicode 数字，如：X56、group1、_x23、i、өԑ12。

以下是无效的标识符： 

- 1ab（以数字开头）
- case（Go 语言的关键字）
- a+b（运算符是不允许的）

`_` 本身就是一个特殊的标识符，被称为空白标识符。它可以像其他标识符那样用于变量的声明或赋值（任何类型都可以赋值给它），但任何赋给这个标识符的值都将被抛弃，因此这些值不能在后续的代码中使用，也不可以使用这个标识符作为变量对其它变量进行赋值或运算。

在编码过程中，你可能会遇到没有名称的变量、类型或方法。虽然这不是必须的，但有时候这样做可以极大地增强代码的灵活性，这些变量被统称为匿名变量。

下面列举了 Go 代码中会使用到的 25 个关键字或保留字：

| break    | default     | func   | interface | select |
| -------- | ----------- | ------ | --------- | ------ |
| case     | defer       | go     | map       | struct |
| chan     | else        | goto   | package   | switch |
| const    | fallthrough | if     | range     | type   |
| continue | for         | import | return    | var    |

之所以刻意地将 Go 代码中的关键字保持的这么少，是为了简化在编译过程第一步中的代码解析。和其它语言一样，关键字不能够作标识符使用。

除了以上介绍的这些关键字，Go 语言还有 36 个预定义标识符，其中包含了基本类型的名称和一些基本的内置函数（第 6.5 节），它们的作用都将在接下来的章节中进行进一步地讲解。

| append | bool    | byte    | cap     | close  | complex | complex64 | complex128 | uint16  |
| ------ | ------- | ------- | ------- | ------ | ------- | --------- | ---------- | ------- |
| copy   | false   | float32 | float64 | imag   | int     | int8      | int16      | uint32  |
| int32  | int64   | iota    | len     | make   | new     | nil       | panic      | uint64  |
| print  | println | real    | recover | string | true    | uint      | uint8      | uintptr |

程序一般由关键字、常量、变量、运算符、类型和函数组成。

程序中可能会使用到这些分隔符：括号 `()`，中括号 `[]` 和大括号 `{}`。

程序中可能会使用到这些标点符号：`.`、`,`、`;`、`:` 和 `…`。

程序的代码通过语句来实现结构化。每个语句不需要像 C 家族中的其它语言一样以分号 `;` 结尾，因为这些工作都将由 Go 编译器自动完成。

如果你打算将多个语句写在同一行，它们则必须使用 `;` 人为区分，但在实际开发中我们并不鼓励这种做法。

​	**包名**

当一个包被导入后，包名就会成了内容的访问器。在

```
import "bytes"

```

之后，被导入的包就能通过 `bytes.Buffer` 来引用了。 若所有人都以相同的名称来引用其内容将大有裨益， 这也就意味着包应当有个恰当的名称：其名称应该简洁明了而易于理解。按照惯例， 包应当以小写的单个单词来命名，且不应使用下划线或驼峰记法。`err` 的命名就是出于简短考虑的，因为任何使用该包的人都会键入该名称。 不必担心**引用次序**的冲突。包名就是导入时所需的唯一默认名称， 它并不需要在所有源码中保持唯一，即便在少数发生冲突的情况下， 也可为导入的包选择一个别名来局部使用。 无论如何，通过文件名来判定使用的包，都是不会产生混淆的。

另一个约定就是包名应为其源码目录的基本名称。在 `src/pkg/encoding/base64` 中的包应作为 `"encoding/base64"` 导入，其包名应为 `base64`， 而非 `encoding_base64` 或 `encodingBase64`。

包的导入者可通过包名来引用其内容，因此包中的可导出名称可以此来避免冲突。 （请勿使用 `import .` 记法，它可以简化必须在被测试包外运行的测试， 除此之外应尽量避免使用。）例如，`bufio` 包中的缓存读取器类型叫做 `Reader` 而非 `BufReader`，因为用户将它看做 `bufio.Reader`，这是个清楚而简洁的名称。 此外，由于被导入的项总是通过它们的包名来确定，因此 `bufio.Reader` 不会与 `io.Reader` 发生冲突。同样，用于创建 `ring.Ring` 的新实例的函数（这就是Go中的**构造函数**）一般会称之为 `NewRing`，但由于 `Ring` 是该包所导出的唯一类型，且该包也叫 `ring`，因此它可以只叫做 `New`，它跟在包的后面，就像 `ring.New`。使用包结构可以帮助你选择好的名称。

另一个简短的例子是 `once.Do`，`once.Do(setup)` 表述足够清晰， 使用 `once.DoOrWaitUntilDone(setup)` 完全就是画蛇添足。 长命名并不会使其更具可读性。一份有用的说明文档通常比额外的长名更有价值。

​	**property**

​	Go并不对获取器（getter）和设置器（setter）提供自动支持。 你应当自己提供获取器和设置器，通常很值得这样做，但若要将 `Get` 放到获取器的名字中，既不符合习惯，也没有必要。若你有个名为 `owner` （小写，未导出）的字段，其获取器应当名为 `Owner`（大写，可导出）而非 `GetOwner`。大写字母即为可导出的这种规定为区分方法和字段提供了便利。 若要提供设置器方法，`SetOwner` 是个不错的选择。两个命名看起来都很合理：

```
owner := obj.Owner()
if owner != user {
	obj.SetOwner(user)
}	
```

​	**接口名**

​	按照约定，只包含一个方法的接口应当以该方法的名称加上-er后缀或类似的修饰来构造一个施动着名词，如 `Reader`、`Writer`、 `Formatter`、`CloseNotifier` 等。

​	诸如此类的命名有很多，遵循它们及其代表的函数名会让事情变的简单。 `Read`、`Write`、`Close`、`Flush`、 `String` 等都具有典型的签名和意义。为避免冲突，请不要用这些名称为你的方法命名， 除非你明确知道它们的签名和意义相同。反之，若你的类型实现了的方法， 与一个众所周知的类型的方法拥有相同的含义，那就使用相同的命名。 请将字符串转换方法命名为 `String` 而非 `ToString`。

​	











##### 1.2 基本概念

###### 	1.2.1 包

​	

​	每个 Go 程序都是由包组成的。
​	程序运行的入口是包 `main` 。
​	这个程序使用并导入了包 `"fmt"` 和 `"math/rand"` 。
​	按照惯例，包名与导入路径的最后一个目录一致。例如，`"math/rand"` 包由 `package rand` 语句开始。
​	**注意**：这个程序的运行环境是确定性的，因此 `rand.Intn` 每次都会返回相同的数字。 （为了得到不同的随机数，需要提供一个随机数种子，参阅 [`rand.Seed`](https://go-zh.org/pkg/math/rand/#Seed)。）

​	包是结构化代码的一种方式：每个程序都由包（通常简称为 pkg）的概念组成，可以使用自身的包或者从其它包中导入内容。

​	如同其它一些编程语言中的类库或命名空间的概念，每个 Go 文件都属于且仅属于一个包。一个包可以由许多以 `.go` 为扩展名的源文件组成，因此文件名和包名一般来说都是不相同的。

​	你必须在源文件中非注释的第一行指明这个文件属于哪个包，如：`package main`。`package main`表示一个可独立执行的程序，每个 Go 应用程序都包含一个名为 `main` 的包。

​	一个应用程序可以包含不同的包，而且即使你只使用 main 包也不必把所有的代码都写在一个巨大的文件里：你可以用一些较小的文件，并且在每个文件非注释的第一行都使用 `package main` 来指明这些文件都属于 main 包。如果你打算编译包名不是为 main 的源文件，如 `pack1`，编译后产生的对象文件将会是 `pack1.a` 而不是可执行程序。另外要注意的是，所有的包名都应该使用小写字母。

​	**标准库**

​	在 Go 的安装文件里包含了一些可以直接使用的包，即标准库。在 Windows 下，标准库的位置在 Go 根目录下的子目录 `pkg\windows_386` 中；在 Linux 下，标准库在 Go 根目录下的子目录 `pkg\linux_amd64` 中（如果是安装的是 32 位，则在 `linux_386` 目录中）。一般情况下，标准包会存放在 `$GOROOT/pkg/$GOOS_$GOARCH/` 目录下。

​	Go 的标准库包含了大量的包（如：fmt 和 os），但是你也可以创建自己的包（第 8 章）。

​	如果想要构建一个程序，则包和包内的文件都必须以正确的顺序进行编译。包的依赖关系决定了其构建顺序。

​	属于同一个包的源文件必须全部被一起编译，一个包即是编译时的一个单元，因此根据惯例，每个目录都只包含一个包。

​	如果对一个包进行更改或重新编译，所有引用了这个包的客户端程序都必须全部重新编译。

​	Go 中的包模型采用了显式依赖关系的机制来达到快速编译的目的，编译器会从后缀名为 `.o` 的对象文件（需要且只需要这个文件）中提取传递依赖类型的信息。

如果 `A.go` 依赖 `B.go`，而 `B.go` 又依赖 `C.go`：

- 编译 `C.go`, `B.go`, 然后是 `A.go`.
- 为了编译 `A.go`, 编译器读取的是 `B.o` 而不是 `C.o`.

这种机制对于编译大型的项目时可以显著地提升编译速度。

​	每一段代码只会被编译一次

一个 Go 程序是通过 `import` 关键字将一组包链接在一起。

`import "fmt"` 告诉 Go 编译器这个程序需要使用 `fmt` 包（的函数，或其他元素），`fmt` 包实现了格式化 IO（输入/输出）的函数。包名被封闭在半角双引号 `""` 中。如果你打算从已编译的包中导入并加载公开声明的方法，不需要插入已编译包的源代码。

如果需要多个包，它们可以被分别导入：

```
import "fmt"
import "os"
```

或：

```
import "fmt"; import "os"
```

但是还有更短且更优雅的方法（被称为因式分解关键字，该方法同样适用于 const、var 和 type 的声明或定义）：

```
import (
   "fmt"
   "os"
)
```

它甚至还可以更短的形式，但使用 gofmt 后将会被强制换行：

```
import ("fmt"; "os")
```

当你导入多个包时，导入的顺序会按照字母排序。

如果包名不是以 `.` 或 `/` 开头，如 `"fmt"` 或者 `"container/list"`，则 Go 会在全局文件进行查找；如果包名以 `./` 开头，则 Go 会在相对目录中查找；如果包名以 `/` 开头（在 Windows 下也可以这样使用），则会在系统的绝对路径中查找。

导入包即等同于包含了这个包的所有的代码对象。

除了符号 `_`，包中所有代码对象的标识符必须是唯一的，以避免名称冲突。但是相同的标识符可以在不同的包中使用，因为可以使用包名来区分它们。

包通过下面这个被编译器强制执行的规则来决定是否将自身的代码对象暴露给外部文件：

**可见性规则**

​	当标识符（包括常量、变量、类型、函数名、结构字段等等）以一个大写字母开头，如：Group1，那么使用这种形式的标识符的对象就可以被外部包的代码所使用（客户端程序需要先导入这个包），这被称为导出（像面向对象语言中的 public）；标识符如果以小写字母开头，则对包外是不可见的，但是他们在整个包的内部是可见并且可用的（像面向对象语言中的 private ）。

（大写字母可以使用任何 Unicode 编码的字符，比如希腊文，不仅仅是 ASCII 码中的大写字母）。

​	因此，在导入一个外部包后，能够且只能够访问该包中导出的对象。

​	假设在包 pack1 中我们有一个变量或函数叫做 Thing（以 T 开头，所以它能够被导出），那么在当前包中导入 pack1 包，Thing 就可以像面向对象语言那样使用点标记来调用：`pack1.Thing`（pack1 在这里是不可以省略的）。

​	因此包也可以作为命名空间使用，帮助避免命名冲突（名称冲突）：两个包中的同名变量的区别在于他们的包名，例如 `pack1.Thing` 和 `pack2.Thing`。

​	你可以通过使用包的别名来解决包名之间的名称冲突，或者说根据你的个人喜好对包名进行重新设置，如：`import fm "fmt"`。下面的代码展示了如何使用包的别名：

```
package main

import fm "fmt" // alias3

func main() {
   fm.Println("hello, world")
}
```

​	**注意事项**

​	如果你导入了一个包却没有使用它，则会在构建程序时引发错误，如 `imported and not used: os`，这正是遵循了 Go 的格言：“没有不必要的代码！“。

​	**包的分级声明和初始化**

​	你可以在使用 `import` 导入包之后定义或声明 0 个或多个常量（const）、变量（var）和类型（type），这些对象的作用域都是全局的（在本包范围内），所以可以被本包中所有的函数调用（如 [gotemplate.go](https://github.com/Unknwon/the-way-to-go_ZH_CN/blob/master/eBook/examples/chapter_4/gotemplate.go) 源文件中的 c 和 v），然后声明一个或多个函数（func）。

```go
package main

import (
	"fmt"
	"math/rand"
)

func main() {
	fmt.Println("My favorite number is", rand.Intn(10))
}
```

​	**导入**
​	这个代码用圆括号组合了导入，这里是打包导入语句。
​	同样可以编写多个导入语句，例如：
```
import "fmt"
import "math"
```

​	**注意**:不推荐分开导入的方式
```go
package main

import (
	"fmt"
	"math"
)

func main() {
	fmt.Printf("Now you have %g problems.", math.Sqrt(7))
}
```

​	**导出**
​	Go语言中，首字母大写的名称是可以导出的。
​	在导入包之后，你只能访问包所导出的名字，任何未导出的名字是不能被包外的代码访问的。
Foo 和 FOO都是被导出的名称。名称 foo是不会被导出的。执行代码，注意编译器报的错误。然后将 `math.pi` 改名为 `math.Pi` 再试着执行一下。
```go
package main

import (
	"fmt"
	"math"
)

func main() {
	fmt.Println(math.pi)
}
```

1.2.2 函数

​	函数可以没有参数或接受多个参数
​	在这个例子中， add接受两个int类型的参数。
​	**注意**:类型在变量名之后。（参考 [这篇关于 Go 语法定义的文章](https://blog.go-zh.org/gos-declaration-syntax)了解类型以这种形式出现的原因。）
```go
package main

import "fmt"

func add(x int, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}

```

当两个或多个连续的函数命名参数是统一类型，则除了最后一个类型之外，其他都可以省略。
在这个例子中：
`x int, y int`

可以被缩写为：
`x,y int`
```go
package main

import "fmt"

func add(x, y int) int {
	return x + y
}

func main() {
	fmt.Println(add(42, 13))
}
```

​	**多值返回**
​	函数可以返回任意数量的返回值
​	swap函数返回了两个字符串。
```go
package main

import "fmt"

func swap(x, y string) (string, string) {
	return y, x
}

func main() {
	a, b := swap("hello", "world")
	fmt.Println(a, b)
}
```

​	**命名返回值**
​	Go的返回值可以被命名，并且就像在函数体楷体声明的变量那样使用。
​	返回值的名称应当具有一定的意义，可以作为文档使用。
​	没有参数的return语句返回哥哥返回变量的当前值，这种做法被称作“裸"返回。
​	直接返回语句仅应当用在像下面这样的短函数中。在上函数中它们会影响代码的可读性。
```go
package main

import "fmt"

func split(sum int) (x, y int) {
	x = sum * 4 / 9
	y = sum - x
	return
}

func main() {
	fmt.Println(split(17))
}
```

1.2.3  变量

​	var 语句定义了一个变量列表：跟函数的列表一样，类型在后面。
​	就像在这个例子中看到的意义，var语句可以定义在包或者函数级别。

```go
package main

import "fmt"

var c, python, java bool

func main() {
	var i int
	fmt.Println(i, c, python, java)
}
```

​	**初始化变量**

​	变量定义可以包含初始值，每个变量对应一个。
​	如果初始化是使用表达式，则可以省略类型；变量从初始值中获得类型。
```go
package main

import "fmt"

var i, j int = 1, 2

func main() {
	var c, python, java = true, false, "no!"
	fmt.Println(i, j, c, python, java)
}
```

​	**短声明变量**
​	在函数中，`:=`简洁赋值语句在明确类型的地方，可以用于替代var定义。
​	函数外的每个语句都必须以关键字开始(var、func等)，`:=`结构不能在函数外使用。
```go
package main

import "fmt"

func main() {
	var i, j int = 1, 2
	k := 3
	c, python, java := true, false, "no!"

	fmt.Println(i, j, k, c, python, java)
}
```

​	**基本类型**
​	Go的基本类型有`Basic types`
```go
bool

string

int  int8  int16  int32  int64
uint uint8 uint16 uint32 uint64 uintptr

byte // uint8 的别名

rune // int32 的别名
     // 代表一个Unicode码

float32 float64

complex64 complex128
```

​	这个例子演示了具有不同类型的变量。 同时与导入语句一样，变量的定义“打包”在一个语法块中。
`int`，`uint` 和 `uintptr` 类型在32位的系统上一般是32位，而在64位系统上是64位。当你需要使用一个整数类型时，你应该首选 `int`，仅当有特别的理由才使用定长整数类型或者无符号整数类型。
```go
package main

import (
	"fmt"
	"math/cmplx"
)

var (
	ToBe   bool       = false
	MaxInt uint64     = 1<<64 - 1
	z      complex128 = cmplx.Sqrt(-5 + 12i)
)

func main() {
	const f = "%T(%v)\n"
	fmt.Printf(f, ToBe, ToBe)
	fmt.Printf(f, MaxInt, MaxInt)
	fmt.Printf(f, z, z)
}
```

​	**零值**
​	变量在定义时没有明确的初始化会赋值为零值。
​	零值是：
​	数值类型为０，布尔类型为false，字符串为""（空字符串）。
```go
package main

import "fmt"

func main() {
	var i int
	var f float64
	var b bool
	var s string
	fmt.Printf("%v %v %v %q\n", i, f, b, s)
}
```

​	**类型转换**
​	表达式T(v)将值转换为类型T。
​	一些关于数值的转换：
```go
var i int = 42
var f float64 = float64(i)
var u uint = uint(f)
```

​	或者，更加简单的形式：
```go
i := 42
f := float64(i)
u := uint(f)
```

​	与C不同的是Go在不同类型之间赋值需要显示转换。
```go
package main

import (
	"fmt"
	"math"
)

func main() {
	var x, y int = 3, 4
	var f float64 = math.Sqrt(float64(x*x + y*y))
	var z uint = uint(f)
	fmt.Println(x, y, z)
}
```

​	**类型推导**

​	在定义一个变量却不显示指定其类型时（使用 `:=` 语法或者 `var =` 表达式语法）， 变量的类型由（等号）右侧的值推导得出。

当右值定义了类型时，新变量的类型与其相同：

```
var i int
j := i // j 也是一个 int
```

但是当右边包含了未指名类型的数字常量时，新的变量就可能是 `int` 、 `float64` 或 `complex128` 。 这取决于常量的精度：

```
i := 42           // int
f := 3.142        // float64
g := 0.867 + 0.5i // complex128
```

尝试修改演示代码中 `v` 的初始值，并观察这是如何影响其类型的。

```go
package main

import "fmt"

func main() {
	v := 42 // change me!
	fmt.Printf("v is of type %T\n", v)
}
```

​	

###### 1.2.1 常量

​	常量的定义与变量类似，只不过使用 `const` 关键字。

​	常量可以是字符、字符串、布尔或数字类型的值。

​	常量不能使用 `:=` 语法定义。

```go
package main

import "fmt"

const Pi = 3.14

func main() {
	const World = "世界"
	fmt.Println("Hello", World)
	fmt.Println("Happy", Pi, "Day")

	const Truth = true
	fmt.Println("Go rules?", Truth)
}
```

​	**数值常量**

​	数值常量是高精度的 *值* 。

​	一个未指定类型的常量由上下文来决定其类型。

​	也尝试一下输出 `needInt(Big)` 吧。

​	（`int` 可以存放最大64位的整数，根据平台不同有时会更少。）

```go
package main

import "fmt"

const (
	Big   = 1 << 100
	Small = Big >> 99
)

func needInt(x int) int { return x*10 + 1 }
func needFloat(x float64) float64 {
	return x * 0.1
}

func main() {
	fmt.Println(needInt(Small))
	fmt.Println(needFloat(Small))
	fmt.Println(needFloat(Big))
}
```

常量使用关键字 `const` 定义，用于存储不会改变的数据。

存储在常量中的数据类型只可以是布尔型、数字型（整数型、浮点型和复数）和字符串型。

常量的定义格式：`const identifier [type] = value`，例如：

```
const Pi = 3.14159
```

在 Go 语言中，你可以省略类型说明符 `[type]`，因为编译器可以根据变量的值来推断其类型。

- 显式类型定义： `const b string = "abc"`
- 隐式类型定义： `const b = "abc"`

一个没有指定类型的常量被使用时，会根据其使用环境而推断出它所需要具备的类型。换句话说，未定义类型的常量会在必要时刻根据上下文来获得相关类型。

```
var n int
f(n + 5) // 无类型的数字型常量 “5” 它的类型在这里变成了 int
```

常量的值必须是能够在编译时就能够确定的；你可以在其赋值表达式中涉及计算过程，但是所有用于计算的值必须在编译期间就能获得。

- 正确的做法：`const c1 = 2/3`
- 错误的做法：`const c2 = getNumber()` // 引发构建错误: `getNumber() used as value`

**因为在编译期间自定义函数均属于未知，因此无法用于常量的赋值，但内置函数可以使用，如：len()。**

数字型的常量是没有大小和符号的，并且可以使用任何精度而不会导致溢出：

```
const Ln2= 0.693147180559945309417232121458\
            176568075500134360255254120680009
const Log2E= 1/Ln2 // this is a precise reciprocal
const Billion = 1e9 // float constant
const hardEight = (1 << 100) >> 97
```

根据上面的例子我们可以看到，反斜杠 `\` 可以在常量表达式中作为多行的连接符使用。

与各种类型的数字型变量相比，你无需担心常量之间的类型转换问题，因为它们都是非常理想的数字。

不过需要注意的是，当常量赋值给一个精度过小的数字型变量时，可能会因为无法正确表达常量所代表的数值而导致溢出，这会在编译期间就引发错误。另外，常量也允许使用并行赋值的形式：

```
const beef, two, c = "eat", 2, "veg"
const Monday, Tuesday, Wednesday, Thursday, Friday, Saturday = 1, 2, 3, 4, 5, 6
const (
    Monday, Tuesday, Wednesday = 1, 2, 3
    Thursday, Friday, Saturday = 4, 5, 6
)
```

常量还可以用作枚举：

```
const (
    Unknown = 0
    Female = 1
    Male = 2
)
```

现在，数字 0、1 和 2 分别代表未知性别、女性和男性。这些枚举值可以用于测试某个变量或常量的实际值，比如使用 switch/case 结构 (第 5.3 节).

在这个例子中，`iota` 可以被用作枚举值：

```
const (
    a = iota
    b = iota
    c = iota
)
```

第一个 `iota` 等于 0，每当 `iota` 在新的一行被使用时，它的值都会自动加 1；所以 `a=0, b=1, c=2` 可以简写为如下形式：

```
const (
    a = iota
    b
    c
)
```

（ **译者注：关于 iota 的使用涉及到非常复杂多样的情况，这里作者解释的并不清晰，因为很难对 iota 的用法进行直观的文字描述。如希望进一步了解，请观看视频教程 《Go编程基础》 第四课：常量与运算符** ）

`iota` 也可以用在表达式中，如：`iota + 50`。在每遇到一个新的常量块或单个常量声明时， `iota` 都会重置为 0（ **简单地讲，每遇到一次 const 关键字，iota 就重置为 0** ）。

当然，常量之所以为常量就是恒定不变的量，因此我们无法在程序运行过程中修改它的值；如果你在代码中试图修改常量的值则会引发编译错误。

引用 `time` 包中的一段代码作为示例：一周中每天的名称。

```
const (
    Sunday = iota
    Monday
    Tuesday
    Wednesday
    Thursday
    Friday
    Saturday
)
```

你也可以使用某个类型作为枚举常量的类型：

```
type Color int

const (
    RED Color = iota // 0
    ORANGE // 1
    YELLOW // 2
    GREEN // ..
    BLUE
    INDIGO
    VIOLET // 6
)
```

##### 1.3 流程控制##

###### 	1.3.1 for循环##

​	Go 只有一种循环结构—— `for` 循环。

基本的 `for` 循环包含三个由分号分开的组成部分：

- 初始化语句：在第一次循环执行前被执行
- 循环条件表达式：每轮迭代开始前被求值
- 后置语句：每轮迭代后被执行

初始化语句一般是一个短变量声明，这里声明的变量仅在整个 `for` 循环语句可见。

如果条件表达式的值变为 `false`，那么迭代将终止。

*注意*：不像 C，Java，或者 Javascript 等其他语言，`for` 语句的三个组成部分 并不需要用括号括起来，但循环体必须用 `{ }` 括起来。

```go
package main

import "fmt"

func main() {
	sum := 0
	for i := 0; i < 10; i++ {
		sum += i
	}
	fmt.Println(sum)
}
```

循环初始化语句和后置语句都是可选的。

```go
package main

import "fmt"

func main() {
	sum := 1
	for ; sum < 1000; {
		sum += sum
	}
	fmt.Println(sum)
}
```

C 的 `while` 在 Go 中叫做 `for` 。

```go
package main

import "fmt"

func main() {
	sum := 1
	for sum < 1000 {
		sum += sum
	}
	fmt.Println(sum)
}
```

如果省略了循环条件，循环就不会结束，因此可以用更简洁地形式表达死循环。

```go
package main

func main() {
	for {
	}
}
```



###### 1.3.2 if判断##

就像 `for` 循环一样，Go 的 `if` 语句也不要求用 `( )` 将条件括起来，同时， `{ }` 还是必须有的。

```go
package main

import (
	"fmt"
	"math"
)

func sqrt(x float64) string {
	if x < 0 {
		return sqrt(-x) + "i"
	}
	return fmt.Sprint(math.Sqrt(x))
}

func main() {
	fmt.Println(sqrt(2), sqrt(-4))
}
```

跟 `for` 一样， `if` 语句可以在条件之前执行一个简单语句。

由这个语句定义的变量的作用域仅在 `if` 范围之内。

（在最后的 `return` 语句处使用 `v` 看看。）

```go
package main

import (
	"fmt"
	"math"
)

func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	}
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}
```

在 `if` 的便捷语句定义的变量同样可以在任何对应的 `else` 块中使用。

（提示：两个 `pow` 调用都在 `main` 调用 `fmt.Println` 前执行完毕了。）

```go
package main

import (
	"fmt"
	"math"
)

func pow(x, n, lim float64) float64 {
	if v := math.Pow(x, n); v < lim {
		return v
	} else {
		fmt.Printf("%g >= %g\n", v, lim)
	}
	// 这里开始就不能使用 v 了
	return lim
}

func main() {
	fmt.Println(
		pow(3, 2, 10),
		pow(3, 3, 20),
	)
}
```

作为练习函数和循环的简单途径，用牛顿法实现开方函数。

在这个例子中，牛顿法是通过选择一个初始点 *z* 然后重复这一过程求 `Sqrt(x)` 的近似值：

$z=z-(z^2 -x)/2z$

为了做到这个，只需要重复计算 10 次，并且观察不同的值（1，2，3，……）是如何逐步逼近结果的。 然后，修改循环条件，使得当值停止改变（或改变非常小）的时候退出循环。观察迭代次数是否变化。结果与 [math.Sqrt](https://go-zh.org/pkg/math/#Sqrt) 接近吗？

提示：定义并初始化一个浮点值，向其提供一个浮点语法或使用转换：

```
z := float64(1)
z := 1.0
```



###### 1.3.3 switch语句##

​	`switch` 语句除非以 `fallthrough` 语句结束，否则分支会自动终止。

```go
package main

import (
	"fmt"
	"runtime"
)

func main() {
	fmt.Print("Go runs on ")
	switch os := runtime.GOOS; os {
	case "darwin":
		fmt.Println("OS X.")
	case "linux":
		fmt.Println("Linux.")
	default:
		// freebsd, openbsd,
		// plan9, windows...
		fmt.Printf("%s.", os)
	}
}
```

switch 的条件从上到下的执行，当匹配成功的时候停止。

（例如，

```
switch i {
case 0:
case f():
}
```

当 `i==0` 时不会调用 `f` 。）

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	fmt.Println("When's Saturday?")
	today := time.Now().Weekday()
	switch time.Saturday {
	case today + 0:
		fmt.Println("Today.")
	case today + 1:
		fmt.Println("Tomorrow.")
	case today + 2:
		fmt.Println("In two days.")
	default:
		fmt.Println("Too far away.")
	}
}
```

没有条件的 switch 同 `switch true` 一样。

这一构造使得可以用更清晰的形式来编写长的 if-then-else 链。

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	t := time.Now()
	switch {
	case t.Hour() < 12:
		fmt.Println("Good morning!")
	case t.Hour() < 17:
		fmt.Println("Good afternoon.")
	default:
		fmt.Println("Good evening.")
	}
}
```



###### 1.3.4 defer语句

​	defer 语句会延迟函数的执行直到上层函数返回。延迟调用的参数会立刻生成，但是在上层函数返回前函数都不会被调用。

```go
package main

import "fmt"

func main() {
	defer fmt.Println("world")

	fmt.Println("hello")
}
```

延迟的函数调用被压入一个栈中。当函数返回时， 会按照后进先出的顺序调用被延迟的函数调用。

阅读[博文](http://blog.go-zh.org/defer-panic-and-recover)了解更多关于 `defer` 语句的信息。

```go
package main

import "fmt"

func main() {
	fmt.Println("counting")

	for i := 0; i < 10; i++ {
		defer fmt.Println(i)
	}

	fmt.Println("done")
}
```



##### 1.4 复杂类型####

###### 	1.4.1 指针#

​	Go 具有指针。 指针保存了变量的内存地址。

​	类型 `*T` 是指向类型 `T` 的值的指针。其零值是 `nil` 。

```
var p *int
```

​	`&` 符号会生成一个指向其作用对象的指针。

```
i := 42
p = &i
```

​	`*` 符号表示指针指向的底层的值。

```
fmt.Println(*p) // 通过指针 p 读取 i
*p = 21         // 通过指针 p 设置 i
```

​	这也就是通常所说的“间接引用”或“非直接引用”，－－与 C 不同，Go 没有指针运算。

```go
package main

import "fmt"

func main() {
	i, j := 42, 2701

	p := &i         // point to i
	fmt.Println(*p) // read i through the pointer
	*p = 21         // set i through the pointer
	fmt.Println(i)  // see the new value of i

	p = &j         // point to j
	*p = *p / 37   // divide j through the pointer
	fmt.Println(j) // see the new value of j
}
```

###### 	1.4.2 结构体

​	一个结构体（struct）就是一个字段的集合，type的含义跟其字面意思相符。

```go
package main

import "fmt"

type Vertex struct {
	X int
	Y int
}

func main() {
	fmt.Println(Vertex{1, 2})
}
```

​	结构体字段使用点号来访问。

```go
package main

import "fmt"

type Vertex struct {
	X int
	Y int
}

func main() {
	v := Vertex{1, 2}
	v.X = 4
	fmt.Println(v.X)
}
```

​	结构体字段可以通过结构体指针来访问，通过指针间接的访问是透明的。

```go
package main

import "fmt"

type Vertex struct {
	X int
	Y int
}

func main() {
	v := Vertex{1, 2}
	p := &v
	p.X = 1e9
	fmt.Println(v)
}
```

​	结构体文法表示通过结构体字段的值作为列表来分配一个结构体。

​	使用Name:语法可以仅列出部分字段。（字段名的顺序无关）

​	特殊的前缀＆返回一个指向结构体的指针	。

```go
package main

import "fmt"

type Vertex struct {
	X, Y int
}

var (
	v1 = Vertex{1, 2}  // 类型为 Vertex
	v2 = Vertex{X: 1}  // Y:0 被省略
	v3 = Vertex{}      // X:0 和 Y:0
	p  = &Vertex{1, 2} // 类型为 *Vertex
)

func main() {
	fmt.Println(v1, p, v2, v3)
}
```

###### 	1.4.3 数组

​	类型[n]T是一个有n个类型为T的值的数组。

​	表达式：

​	`var a[10]int`

​	定义变量a是一个有十个整数的数组。

​	数组的长度是其类型的一部分，因此数组不能改变大小。

```go
package main

import "fmt"

func main() {
	var a [2]string
	a[0] = "Hello"
	a[1] = "World"
	fmt.Println(a[0], a[1])
	fmt.Println(a)
}
```

###### 	1.4.4 slice

​	一个slice会指向一个序列的值，并且包含了长度信息。

​	[]T是一个元素类型为T的slice。

​	len(s)返回slice s的长度。

```go
package main

import "fmt"

func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	fmt.Println("s ==", s)

	for i := 0; i < len(s); i++ {
		fmt.Printf("s[%d] == %d\n", i, s[i])
	}
}
```

​	slice可以包含任意的类型，包括另一个slice。

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	// Create a tic-tac-toe board.
	game := [][]string{
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
		[]string{"_", "_", "_"},
	}

	// The players take turns.
	game[0][0] = "X"
	game[2][2] = "O"
	game[2][0] = "X"
	game[1][0] = "O"
	game[0][2] = "X"

	printBoard(game)
}

func printBoard(s [][]string) {
	for i := 0; i < len(s); i++ {
		fmt.Printf("%s\n", strings.Join(s[i], " "))
	}
}
```

​	slice可以重新切片，创建一个新的slice值指向相同的数组。

​	表达式：

​	`s[lo:hi]`

​	表示从lo到hi-1的ｓｌｉｃｅ元素，含前端，不包含后端，因此

​	`s[lo:lo]`

​	是空的，而`s[lo:lo+1]`有一个元素。

```go
package main

import "fmt"

func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	fmt.Println("s ==", s)
	fmt.Println("s[1:4] ==", s[1:4])

	// 省略下标代表从 0 开始
	fmt.Println("s[:3] ==", s[:3])

	// 省略上标代表到 len(s) 结束
	fmt.Println("s[4:] ==", s[4:])
}
```

​	slice由函数make创建，这会分配一个全是零值的数组并且返回一个slice指向这个数组：

​	`a := make([]int, 5) // len(a) = 5`

​	为了指定容量，可传递第三个参数到make：

```go
b := make([]int, 0, 5) //len(b) = 0, cap(b) = 5
b = b[:cap(b)] //len(b) = 5, cap(b) = 5
b = b[1:] // len(b) = 4, cap(b) = 4
```

```go
package main

import "fmt"

func main() {
	a := make([]int, 5)
	printSlice("a", a)
	b := make([]int, 0, 5)
	printSlice("b", b)
	c := b[:2]
	printSlice("c", c)
	d := c[2:5]
	printSlice("d", d)
}

func printSlice(s string, x []int) {
	fmt.Printf("%s len=%d cap=%d %v\n",
		s, len(x), cap(x), x)
}
```

slice的零值是nil。一个nil的slice的长度和容量是0。

```go
package main

import "fmt"

func main() {
	var z []int
	fmt.Println(z, len(z), cap(z))
	if z == nil {
		fmt.Println("nil!")
	}
}
```

向slice的末尾添加元素是一种常见的操作，因此Go提供了一个内建函数append。内建函数的文档对append做了详细解释。

`func append(s []T, vs ...T)[]T`

append的第一个参数是一个元素类型为T的slice，其余类型为T的值将会附件到该slice的末尾。

append的结果是一个包含原slice所有元素加上新添加元素的slice。

如果s的底层数组太小，而不能容纳所有值时，会分配一个更大的数组。返回的slice会指向这个新分配的数组。（了解更多关于 slice 的内容，参阅文章[Go 切片：用法和本质](https://blog.go-zh.org/go-slices-usage-and-internals)。）

```go
package main

import "fmt"

func main() {
	var a []int
	printSlice("a", a)

	// append works on nil slices.
	a = append(a, 0)
	printSlice("a", a)

	// the slice grows as needed.
	a = append(a, 1)
	printSlice("a", a)

	// we can add more than one element at a time.
	a = append(a, 2, 3, 4)
	printSlice("a", a)
}

func printSlice(s string, x []int) {
	fmt.Printf("%s len=%d cap=%d %v\n",
		s, len(x), cap(x), x)
}
```

###### 	1.4.5 range

​	for循环的range格式可以对slice或者map进行迭代循环。

​	当使用for循环遍历一个slice时，每次迭代range将返回两个值。第一个是当前下表（序号），第二个是该下标所对应元素的一个拷贝。

```go
package main

import "fmt"

func main() {
	var a []int
	printSlice("a", a)

	// append works on nil slices.
	a = append(a, 0)
	printSlice("a", a)

	// the slice grows as needed.
	a = append(a, 1)
	printSlice("a", a)

	// we can add more than one element at a time.
	a = append(a, 2, 3, 4)
	printSlice("a", a)
}

func printSlice(s string, x []int) {
	fmt.Printf("%s len=%d cap=%d %v\n",
		s, len(x), cap(x), x)
}
```

​	可以通过赋值给 `_` 来忽略序号和值。

如果只需要索引值，去掉 “ `, value` ” 的部分即可。

```go
package main

import "fmt"

func main() {
	pow := make([]int, 10)
	for i := range pow {
		pow[i] = 1 << uint(i)
	}
	for _, value := range pow {
		fmt.Printf("%d\n", value)
	}
}
```

**练习**：slice

实现 `Pic` 。它返回一个长度为 `dy` 的 slice，其中每个元素是一个长度为 `dx` 且元素类型为8位无符号整数的 slice。当你运行这个程序时， 它会将每个整数作为对应像素的灰度值（好吧，其实是蓝度）并显示这个 slice 所对应的图像。

计算每个像素的灰度值的方法由你决定；几个有意思的选择包括 `(x+y)/2`、`x*y` 和 `x^y` 。

（需要使用循环来分配 `[][]uint8` 中的每个 `[]uint8` 。）

（使用 `uint8(intValue)` 来在类型之间进行转换。）

###### 	1.4.6 map

​	map映射键到值。

​	map在使用之前必须用make创建；值为的map是空的，并且不能对其赋值。

```go
package main

import "fmt"

type Vertex struct {
	Lat, Long float64
}

var m map[string]Vertex

func main() {
	m = make(map[string]Vertex)
	m["Bell Labs"] = Vertex{
		40.68433, -74.39967,
	}
	fmt.Println(m["Bell Labs"])
}
```

map 的文法跟结构体文法相似，不过必须有键名。

```go
package main

import "fmt"

type Vertex struct {
	Lat, Long float64
}

var m = map[string]Vertex{
	"Bell Labs": Vertex{
		40.68433, -74.39967,
	},
	"Google": Vertex{
		37.42202, -122.08408,
	},
}

func main() {
	fmt.Println(m)
}
```

若顶级类型只是一个类型名，你可以在文法的元素中省略它。

```go
package main

import "fmt"

type Vertex struct {
	Lat, Long float64
}

var m = map[string]Vertex{
	"Bell Labs": {40.68433, -74.39967},
	"Google":    {37.42202, -122.08408},
}

func main() {
	fmt.Println(m)
}
```

在 map `m` 中插入或修改一个元素：

```
m[key] = elem
```

获得元素：

```
elem = m[key]
```

删除元素：

```
delete(m, key)
```

通过双赋值检测某个键存在：

```
elem, ok = m[key]
```

如果 `key` 在 `m` 中， `ok` 为 `true`。否则， `ok` 为 `false`，并且 `elem` 是 map 的元素类型的零值。

同样的，当从 map 中读取某个不存在的键时，结果是 map 的元素类型的零值。

```go
package main

import "fmt"

func main() {
	m := make(map[string]int)

	m["Answer"] = 42
	fmt.Println("The value:", m["Answer"])

	m["Answer"] = 48
	fmt.Println("The value:", m["Answer"])

	delete(m, "Answer")
	fmt.Println("The value:", m["Answer"])

	v, ok := m["Answer"]
	fmt.Println("The value:", v, "Present?", ok)
}
```

**练习**：map

实现 `WordCount`。它应当返回一个含有 `s` 中每个 “词” 个数的 map。函数 `wc.Test` 针对这个函数执行一个测试用例，并输出成功还是失败。

你会发现 [strings.Fields](https://go-zh.org/pkg/strings/#Fields) 很有帮助。

```go
package main

import (
	"golang.org/x/tour/wc"
)

func WordCount(s string) map[string]int {
	return map[string]int{"x": 1}
}

func main() {
	wc.Test(WordCount)
}
```

###### 	1.4.7 函数值

​	函数也是值。他们可以像其他值一样传递，比如，函数值可以作为函数的参数或者返回值。

```go
package main

import (
	"fmt"
	"math"
)

func compute(fn func(float64, float64) float64) float64 {
	return fn(3, 4)
}

func main() {
	hypot := func(x, y float64) float64 {
		return math.Sqrt(x*x + y*y)
	}
	fmt.Println(hypot(5, 12))

	fmt.Println(compute(hypot))
	fmt.Println(compute(math.Pow))
}
```

**练习**：斐波拉契数列

现在来通过函数做些有趣的事情。

实现一个 `fibonacci` 函数，返回一个函数（一个闭包）可以返回连续的斐波纳契数。

```go
package main

import "fmt"

// fibonacci 函数会返回一个返回 int 的函数。
func fibonacci() func() int {
}

func main() {
	f := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(f())
	}
}
```



##### 1.5 方法与接口#####

###### 	1.5.1 方法

​	Go 没有类。然而，仍然可以在结构体类型上定义方法。方法接收者* 出现在 `func` 关键字和方法名之间的参数中。

```go
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := &Vertex{3, 4}
	fmt.Println(v.Abs())
}
```

你可以对包中的 *任意* 类型定义任意方法，而不仅仅是针对结构体。

但是，不能对来自其他包的类型或基础类型定义方法。

```go
package main

import (
	"fmt"
	"math"
)

type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
	}
	return float64(f)
}

func main() {
	f := MyFloat(-math.Sqrt2)
	fmt.Println(f.Abs())
}
```

方法可以与命名类型或命名类型的指针关联。

刚刚看到的两个 `Abs` 方法。一个是在 `*Vertex` 指针类型上，而另一个在 `MyFloat` 值类型上。 有两个原因需要使用指针接收者。首先避免在每个方法调用中拷贝值（如果值类型是大的结构体的话会更有效率）。其次，方法可以修改接收者指向的值。

尝试修改 `Abs` 的定义，同时 `Scale` 方法使用 `Vertex` 代替 `*Vertex` 作为接收者。

当 `v` 是 `Vertex` 的时候 `Scale` 方法没有任何作用。`Scale` 修改 `v`。当 `v` 是一个值（非指针），方法看到的是 `Vertex` 的副本，并且无法修改原始值。

`Abs` 的工作方式是一样的。只不过，仅仅读取 `v`。所以读取的是原始值（通过指针）还是那个值的副本并没有关系。

```go
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := &Vertex{3, 4}
	fmt.Printf("Before scaling: %+v, Abs: %v\n", v, v.Abs())
	v.Scale(5)
	fmt.Printf("After scaling: %+v, Abs: %v\n", v, v.Abs())
}
```

###### 	1.5.2 接口

​	接口类型是由一组方法定义的集合。

接口类型的值可以存放实现这些方法的任何值。

**注意**： 示例代码的 22 行存在一个错误。 由于 `Abs` 只定义在 `*Vertex`（指针类型）上， 所以 `Vertex`（值类型）不满足 `Abser`。	
```go
package main

import (
	"fmt"
	"math"
)

type Abser interface {
	Abs() float64
}

func main() {
	var a Abser
	f := MyFloat(-math.Sqrt2)
	v := Vertex{3, 4}

	a = f  // a MyFloat 实现了 Abser
	a = &v // a *Vertex 实现了 Abser

	// 下面一行，v 是一个 Vertex（而不是 *Vertex）
	// 所以没有实现 Abser。
	a = v

	fmt.Println(a.Abs())
}

type MyFloat float64

func (f MyFloat) Abs() float64 {
	if f < 0 {
		return float64(-f)
```

​	类型通过实现那些方法来实现接口。 没有显式声明的必要；所以也就没有关键字“implements“。

隐式接口解藕了实现接口的包和定义接口的包：互不依赖。

因此，也就无需在每一个实现上增加新的接口名称，这样同时也鼓励了明确的接口定义。

[包 io](https://go-zh.org/pkg/io/) 定义了 `Reader` 和 `Writer`；其实不一定要这么做。

```go
package main

import (
	"fmt"
	"os"
)

type Reader interface {
	Read(b []byte) (n int, err error)
}

type Writer interface {
	Write(b []byte) (n int, err error)
}

type ReadWriter interface {
	Reader
	Writer
}

func main() {
	var w Writer

	// os.Stdout 实现了 Writer
	w = os.Stdout

	fmt.Fprintf(w, "hello, writer\n")
}
```

一个普遍存在的接口是 [`fmt`](https://go-zh.org/pkg/fmt/) 包中定义的 [`Stringer`](https://go-zh.org/pkg/fmt/#Stringer)。

```
type Stringer interface {
    String() string
}
```

`Stringer` 是一个可以用字符串描述自己的类型。`fmt`包 （还有许多其他包）使用这个来进行输出。

```go
package main

import "fmt"

type Person struct {
	Name string
	Age  int
}

func (p Person) String() string {
	return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}

func main() {
	a := Person{"Arthur Dent", 42}
	z := Person{"Zaphod Beeblebrox", 9001}
	fmt.Println(a, z)
}
```

**练习**:Stringers

让 `IPAddr` 类型实现 `fmt.Stringer` 以便用点分格式输出地址。

例如，`IPAddr{1, 2, 3, 4}` 应当输出 `"1.2.3.4"`。

```go
package main

import "fmt"

type IPAddr [4]byte

// TODO: Add a "String() string" method to IPAddr.

func main() {
	addrs := map[string]IPAddr{
		"loopback":  {127, 0, 0, 1},
		"googleDNS": {8, 8, 8, 8},
	}
	for n, a := range addrs {
		fmt.Printf("%v: %v\n", n, a)
	}
}
```

###### 	1.5.3 错误

​	Go 程序使用 `error` 值来表示错误状态。

与 `fmt.Stringer` 类似， `error` 类型是一个内建接口：

```
type error interface {
    Error() string
}
```

（与 `fmt.Stringer` 类似，`fmt` 包在输出时也会试图匹配 `error`。）

通常函数会返回一个 `error` 值，调用的它的代码应当判断这个错误是否等于 `nil`， 来进行错误处理。

```
i, err := strconv.Atoi("42")
if err != nil {
    fmt.Printf("couldn't convert number: %v\n", err)
    return
}
fmt.Println("Converted integer:", i)
```

`error` 为 nil 时表示成功；非 nil 的 `error` 表示错误。

```go
package main

import (
	"fmt"
	"time"
)

type MyError struct {
	When time.Time
	What string
}

func (e *MyError) Error() string {
	return fmt.Sprintf("at %v, %s",
		e.When, e.What)
}

func run() error {
	return &MyError{
		time.Now(),
		"it didn't work",
	}
}

func main() {
	if err := run(); err != nil {
		fmt.Println(err)
	}
}
```

**练习**：错误

由于不支持复数，当 `Sqrt` 接收到一个负数时，应当返回一个非 nil 的错误值。

创建一个新类型

```
type ErrNegativeSqrt float64
```

为其实现

```
func (e ErrNegativeSqrt) Error() string
```

使其成为一个 `error`， 该方法就可以让 `ErrNegativeSqrt(-2).Error()` 返回 `"cannot Sqrt negative number: -2"`。

**注意**：在 `Error` 方法内调用 `fmt.Sprint(e)` 将会让程序陷入死循环。可以通过先转换 `e` 来避免这个问题：`fmt.Sprint(float64(e))`。请思考这是为什么呢？

修改 `Sqrt` 函数，使其接受一个负数时，返回 `ErrNegativeSqrt` 值。

```go
package main

import (
	"fmt"
)

func Sqrt(x float64) (float64, error) {
	return 0, nil
}

func main() {
	fmt.Println(Sqrt(2))
	fmt.Println(Sqrt(-2))
}
```

`io` 包指定了 `io.Reader` 接口， 它表示从数据流结尾读取。

Go 标准库包含了这个接口的[许多实现](https://go-zh.org/search?q=Read#Global)， 包括文件、网络连接、压缩、加密等等。

`io.Reader` 接口有一个 `Read` 方法：

```
func (T) Read(b []byte) (n int, err error)
```

`Read` 用数据填充指定的字节 slice，并且返回填充的字节数和错误信息。 在遇到数据流结尾时，返回 `io.EOF` 错误。

例子代码创建了一个 [`strings.Reader`](https://go-zh.org/pkg/strings/#Reader)。 并且以每次 8 字节的速度读取它的输出。

```go
package main

import (
	"fmt"
	"io"
	"strings"
)

func main() {
	r := strings.NewReader("Hello, Reader!")

	b := make([]byte, 8)
	for {
		n, err := r.Read(b)
		fmt.Printf("n = %v err = %v b = %v\n", n, err, b)
		fmt.Printf("b[:n] = %q\n", b[:n])
		if err == io.EOF {
			break
		}
	}
}
```

**练习**：Reader

实现一个 `Reader` 类型，它不断生成 ASCII 字符 `'A'` 的流。

```go
package main

import "golang.org/x/tour/reader"

type MyReader struct{}

// TODO: Add a Read([]byte) (int, error) method to MyReader.

func main() {
	reader.Validate(MyReader{})
}
```

**练习**：rot13Reader

一个常见模式是 [io.Reader](https://go-zh.org/pkg/io/#Reader) 包裹另一个 `io.Reader`，然后通过某种形式修改数据流。

例如，[gzip.NewReader](https://go-zh.org/pkg/compress/gzip/#NewReader) 函数接受 `io.Reader`（压缩的数据流）并且返回同样实现了 `io.Reader` 的 `*gzip.Reader`（解压缩后的数据流）。

编写一个实现了 `io.Reader` 的 `rot13Reader`， 并从一个 `io.Reader` 读取， 利用 [rot13](https://en.wikipedia.org/wiki/ROT13) 代换密码对数据流进行修改。

已经帮你构造了 `rot13Reader` 类型。 通过实现 `Read` 方法使其匹配 `io.Reader`。

```go
package main

import (
	"io"
	"os"
	"strings"
)

type rot13Reader struct {
	r io.Reader
}

func main() {
	s := strings.NewReader("Lbh penpxrq gur pbqr!")
	r := rot13Reader{s}
	io.Copy(os.Stdout, &r)
}
```

[包 http](https://go-zh.org/pkg/net/http/) 通过任何实现了 `http.Handler` 的值来响应 HTTP 请求：

```
package http

type Handler interface {
    ServeHTTP(w ResponseWriter, r *Request)
}
```

在这个例子中，类型 `Hello` 实现了 `http.Handler`。

访问 [http://localhost:4000/](http://localhost:4000/) 会看到来自程序的问候。

*注意：* 这个例子无法在基于 web 的指南用户界面运行。为了尝试编写 web 服务器，可能需要[安装 Go](https://go-zh.org/doc/install/)。

```go
package main

import (
	"fmt"
	"log"
	"net/http"
)

type Hello struct{}

func (h Hello) ServeHTTP(
	w http.ResponseWriter,
	r *http.Request) {
	fmt.Fprint(w, "Hello!")
}

func main() {
	var h Hello
	err := http.ListenAndServe("localhost:4000", h)
	if err != nil {
		log.Fatal(err)
	}
}
```

**练习**：HTTP处理

实现下面的类型，并在其上定义 ServeHTTP 方法。在 web 服务器中注册它们来处理指定的路径。

```
type String string

type Struct struct {
    Greeting string
    Punct    string
    Who      string
}
```

例如，可以使用如下方式注册处理方法：

```
http.Handle("/string", String("I'm a frayed knot."))
http.Handle("/struct", &Struct{"Hello", ":", "Gophers!"})
```

在启动你的 http 服务器后，你将能够访问： [http://localhost:4000/string](http://localhost:4000/string) 和 [http://localhost:4000/struct](http://localhost:4000/struct).

*注意：* 这个例子无法在基于 web 的用户界面下运行。 为了尝试编写 web 服务，你可能需要 [安装 Go](https://go-zh.org/doc/install/)。

```go
package main

import (
	"log"
	"net/http"
)

func main() {
	// your http.Handle calls here
	log.Fatal(http.ListenAndServe("localhost:4000", nil))
}
```

[Package image](https://go-zh.org/pkg/image/#Image) 定义了 `Image` 接口：

```
package image

type Image interface {
    ColorModel() color.Model
    Bounds() Rectangle
    At(x, y int) color.Color
}
```

**注意**：`Bounds` 方法的 `Rectangle` 返回值实际上是一个 [`image.Rectangle`](https://go-zh.org/pkg/image/#Rectangle)， 其定义在 `image` 包中。

（参阅[文档](https://go-zh.org/pkg/image/#Image)了解全部信息。）

`color.Color` 和 `color.Model` 也是接口，但是通常因为直接使用预定义的实现 `image.RGBA` 和 `image.RGBAModel` 而被忽视了。这些接口和类型由[`image/color` 包](https://go-zh.org/pkg/image/color/)定义。

```go
package main

import (
	"fmt"
	"image"
)

func main() {
	m := image.NewRGBA(image.Rect(0, 0, 100, 100))
	fmt.Println(m.Bounds())
	fmt.Println(m.At(0, 0).RGBA())
}
```

**练习**：图片

自定义的 `Image` 类型，要实现[必要的方法](https://go-zh.org/pkg/image/#Image)，并且调用 `pic.ShowImage`。

`Bounds` 应当返回一个 `image.Rectangle`，例如 `image.Rect(0, 0, w, h)`。

`ColorModel` 应当返回 `color.RGBAModel`。

`At` 应当返回一个颜色；在这个例子里，在最后一个图片生成器的值 `v` 匹配 `color.RGBA{v, v, 255, 255}`。

```go
package main

import "golang.org/x/tour/pic"

type Image struct{}

func main() {
	m := Image{}
	pic.ShowImage(m)
}
```

##### 1.6 并发

###### 	1.6.1 goroutine

​	*goroutine* 是由 Go 运行时环境管理的轻量级线程。

```
go f(x, y, z)
```

开启一个新的 goroutine 执行

```
f(x, y, z)
```

`f`，`x`，`y` 和 `z` 是当前 goroutine 中定义的，但是在新的 goroutine 中运行 `f`。

goroutine 在相同的地址空间中运行，因此访问共享内存必须进行同步。[`sync`](https://go-zh.org/pkg/sync/) 提供了这种可能，不过在 Go 中并不经常用到，因为有其他的办法。（在接下来的内容中会涉及到。）

```go
package main

import (
	"fmt"
	"time"
)

func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
}

func main() {
	go say("world")
	say("hello")
}
```

###### 1.6.2 channel

channel 是有类型的管道，可以用 channel 操作符 <- 对其发送或者接收值。

ch <- v    // 将 v 送入 channel ch。
v := <-ch  // 从 ch 接收，并且赋值给 v。
（“箭头”就是数据流的方向。）

和 map 与 slice 一样，channel 使用前必须创建：

ch := make(chan int)
默认情况下，在另一端准备好之前，发送和接收都会阻塞。这使得 goroutine 可以在没有明确的锁或竞态变量的情况下进行同步。

```go
package main

import "fmt"

func sum(a []int, c chan int) {
	sum := 0
	for _, v := range a {
		sum += v
	}
	c <- sum // 将和送入 c
}

func main() {
	a := []int{7, 2, 8, -9, 4, 0}

	c := make(chan int)
	go sum(a[:len(a)/2], c)
	go sum(a[len(a)/2:], c)
	x, y := <-c, <-c // 从 c 中获取

	fmt.Println(x, y, x+y)
}
```

channel 可以是 *带缓冲的*。为 `make` 提供第二个参数作为缓冲长度来初始化一个缓冲 channel：

```
ch := make(chan int, 100)
```

向带缓冲的 channel 发送数据的时候，只有在缓冲区满的时候才会阻塞。 而当缓冲区为空的时候接收操作会阻塞。

修改例子使得缓冲区被填满，然后看看会发生什么。

```go
package main

import "fmt"

func main() {
	ch := make(chan int, 2)
	ch <- 1
	ch <- 2
	fmt.Println(<-ch)
	fmt.Println(<-ch)
}
```

发送者可以 `close` 一个 channel 来表示再没有值会被发送了。接收者可以通过赋值语句的第二参数来测试 channel 是否被关闭：当没有值可以接收并且 channel 已经被关闭，那么经过

```
v, ok := <-ch
```

之后 `ok` 会被设置为 `false`。

循环 `for i := range c` 会不断从 channel 接收值，直到它被关闭。

*注意：* 只有发送者才能关闭 channel，而不是接收者。向一个已经关闭的 channel 发送数据会引起 panic。 *还要注意：* channel 与文件不同；通常情况下无需关闭它们。只有在需要告诉接收者没有更多的数据的时候才有必要进行关闭，例如中断一个 `range`。

```go
package main

import (
	"fmt"
)

func fibonacci(n int, c chan int) {
	x, y := 0, 1
	for i := 0; i < n; i++ {
		c <- x
		x, y = y, x+y
	}
	close(c)
}

func main() {
	c := make(chan int, 10)
	go fibonacci(cap(c), c)
	for i := range c {
		fmt.Println(i)
	}
}
```

`select` 语句使得一个 goroutine 在多个通讯操作上等待。

`select` 会阻塞，直到条件分支中的某个可以继续执行，这时就会执行那个条件分支。当多个都准备好的时候，会随机选择一个。

```go
package main

import "fmt"

func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
		quit <- 0
	}()
	fibonacci(c, quit)
}
```

`select` 语句使得一个 goroutine 在多个通讯操作上等待。

`select` 会阻塞，直到条件分支中的某个可以继续执行，这时就会执行那个条件分支。当多个都准备好的时候，会随机选择一个。

```go
package main

import "fmt"

func fibonacci(c, quit chan int) {
	x, y := 0, 1
	for {
		select {
		case c <- x:
			x, y = y, x+y
		case <-quit:
			fmt.Println("quit")
			return
		}
	}
}

func main() {
	c := make(chan int)
	quit := make(chan int)
	go func() {
		for i := 0; i < 10; i++ {
			fmt.Println(<-c)
		}
		quit <- 0
	}()
	fibonacci(c, quit)
}
```

当 `select` 中的其他条件分支都没有准备好的时候，`default` 分支会被执行。

为了非阻塞的发送或者接收，可使用 `default` 分支：

```
select {
case i := <-c:
    // 使用 i
default:
    // 从 c 读取会阻塞
}
```

```go
package main

import (
	"fmt"
	"time"
)

func main() {
	tick := time.Tick(100 * time.Millisecond)
	boom := time.After(500 * time.Millisecond)
	for {
		select {
		case <-tick:
			fmt.Println("tick.")
		case <-boom:
			fmt.Println("BOOM!")
			return
		default:
			fmt.Println("    .")
			time.Sleep(50 * time.Millisecond)
		}
	}
}
```

**练习**：等价二叉树

可以用多种不同的二叉树的叶子节点存储相同的数列值。例如，这里有两个二叉树保存了序列 1，1，2，3，5，8，13。

 ![tree](/home/zounengren/图片/tree.png)

用于检查两个二叉树是否存储了相同的序列的函数在多数语言中都是相当复杂的。这里将使用 Go 的并发和 channel 来编写一个简单的解法。

这个例子使用了 `tree` 包，定义了类型：

```
type Tree struct {
    Left  *Tree
    Value int
    Right *Tree
}
```

**1.** 实现 `Walk` 函数。

**2.** 测试 `Walk` 函数。

函数 `tree.New(k)` 构造了一个随机结构的二叉树，保存了值 `k`，`2k`，`3k`，...，`10k`。 创建一个新的 channel `ch` 并且对其进行步进：

```
go Walk(tree.New(1), ch)
```

然后从 channel 中读取并且打印 10 个值。应当是值 1，2，3，...，10。

**3.** 用 `Walk` 实现 `Same` 函数来检测是否 `t1` 和 `t2` 存储了相同的值。

**4.** 测试 `Same` 函数。

`Same(tree.New(1), tree.New(1))` 应当返回 `true`，而 `Same(tree.New(1), tree.New(2))` 应当返回 `false`。

```go
package main

import "golang.org/x/tour/tree"

// Walk 步进 tree t 将所有的值从 tree 发送到 channel ch。
func Walk(t *tree.Tree, ch chan int)

// Same 检测树 t1 和 t2 是否含有相同的值。
func Same(t1, t2 *tree.Tree) bool

func main() {
}
```

###### 	1.6.3 mutex

​	我们已经看到 `channel` 用来在各个 goroutine 间进行通信是非常合适的了。

但是如果我们并不需要通信呢？比如说，如果我们只是想保证在每个时刻，只有一个 goroutine 能访问一个共享的变量从而避免冲突？

这里涉及的概念叫做 *互斥*，通常使用 _互斥锁_(mutex)_来提供这个限制。

Go 标准库中提供了 [`sync.Mutex`](https://go-zh.org/pkg/sync/#Mutex) 类型及其两个方法：

- `Lock`
- `Unlock`

我们可以通过在代码前调用 `Lock` 方法，在代码后调用 `Unlock` 方法来保证一段代码的互斥执行。 参见 `Inc` 方法。

我们也可以用 `defer` 语句来保证互斥锁一定会被解锁。参见 `Value` 方法。

```go
package main

import (
	"fmt"
	"sync"
	"time"
)

// SafeCounter 的并发使用是安全的。
type SafeCounter struct {
	v   map[string]int
	mux sync.Mutex
}

// Inc 增加给定 key 的计数器的值。
func (c *SafeCounter) Inc(key string) {
	c.mux.Lock()
	// Lock 之后同一时刻只有一个 goroutine 能访问 c.v
	c.v[key]++
	c.mux.Unlock()
}

// Value 返回给定 key 的计数器的当前值。
func (c *SafeCounter) Value(key string) int {
	c.mux.Lock()
	// Lock 之后同一时刻只有一个 goroutine 能访问 c.v
	defer c.mux.Unlock()
	return c.v[key]
}

func main() {
	c := SafeCounter{v: make(map[string]int)}
	for i := 0; i < 1000; i++ {
		go c.Inc("somekey")
	}

	time.Sleep(time.Second)
	fmt.Println(c.Value("somekey"))
}
```

**练习**：Web爬虫

在这个练习中，将会使用 Go 的并发特性来并行执行 web 爬虫。

修改 `Crawl` 函数来并行的抓取 URLs，并且保证不重复。

*提示*：你可以用一个 map 来缓存已经获取的 URL，但是需要注意 map 本身并不是并发安全的！

```go
package main

import (
	"fmt"
)

type Fetcher interface {
	// Fetch 返回 URL 的 body 内容，并且将在这个页面上找到的 URL 放到一个 slice 中。
	Fetch(url string) (body string, urls []string, err error)
}

// Crawl 使用 fetcher 从某个 URL 开始递归的爬取页面，直到达到最大深度。
func Crawl(url string, depth int, fetcher Fetcher) {
	// TODO: 并行的抓取 URL。
	// TODO: 不重复抓取页面。
        // 下面并没有实现上面两种情况：
	if depth <= 0 {
		return
	}
	body, urls, err := fetcher.Fetch(url)
	if err != nil {
		fmt.Println(err)
		return
	}
	fmt.Printf("found: %s %q\n", url, body)
	for _, u := range urls {
		Crawl(u, depth-1, fetcher)
	}
	return
}

func main() {
	Crawl("http://golang.org/", 4, fetcher)
}

// fakeFetcher 是返回若干结果的 Fetcher。
type fakeFetcher map[string]*fakeResult

type fakeResult struct {
	body string
	urls []string
}

func (f fakeFetcher) Fetch(url string) (string, []string, error) {
	if res, ok := f[url]; ok {
		return res.body, res.urls, nil
	}
	return "", nil, fmt.Errorf("not found: %s", url)
}

// fetcher 是填充后的 fakeFetcher。
var fetcher = fakeFetcher{
	"http://golang.org/": &fakeResult{
		"The Go Programming Language",
		[]string{
			"http://golang.org/pkg/",
			"http://golang.org/cmd/",
		},
	},
	"http://golang.org/pkg/": &fakeResult{
		"Packages",
		[]string{
			"http://golang.org/",
			"http://golang.org/cmd/",
			"http://golang.org/pkg/fmt/",
			"http://golang.org/pkg/os/",
		},
	},
	"http://golang.org/pkg/fmt/": &fakeResult{
		"Package fmt",
		[]string{
			"http://golang.org/",
			"http://golang.org/pkg/",
		},
	},
	"http://golang.org/pkg/os/": &fakeResult{
		"Package os",
		[]string{
			"http://golang.org/",
			"http://golang.org/pkg/",
		},
	},
}
```
















###					Golang概念介绍###

##### 一、代码现象

实例①

```go
package main
import (
	"fmt"
)
func main() {
	var i int;      	// i 的类型是int型
	var p *int;             // p 的类型是[int型的指针]
    i = 1;           	// i 的值为 1;
	p = &i;                 // p 的值为 [i的地址]
	fmt.Printf("i=%d;p=%d;*p=%d\n",i,p,*p);
	*p = 2;                 // *p 的值为[[i的地址]的指针](其实就是i),这行代码也就等价于 i = 2
	fmt.Printf("i=%d;p=%d;*p=%d\n",i,p,*p);
	i = 3;                  
	fmt.Printf("i=%d;p=%d;*p=%d\n",i,p,*p);
}
```

输出结果为:
```go
i=1;p=859530371808;*p=1
i=2;p=859530371808;*p=2
i=3;p=859530371808;*p=3
```

实例②

```go
package main


import (
	"fmt"
)

type abc struct {
	 v int;
}

func (a abc) aaaa (){
	a.v = 1;
	fmt.Printf("1:%d\n",a.v);
}

func (a *abc) bbbb (){
	fmt.Printf("2:%d\n",a.v);
	a.v = 2;
	fmt.Printf("3:%d\n",a.v);
}

func (a *abc) cccc(){
	fmt.Printf("4:%d\n",a.v);
}

func main() {
	aobj := abc{} // new(abc);
	aobj.aaaa();
	aobj.bbbb();
	aobj.cccc();
}
```

输出结果为:
```go
1:1
2:0
3:2
4:2
```

#####二、现象分析
​	可以看到函数aaaa中,v赋值的1在函数bbbb和cccc里消失了.为什么呢?细心的同学发现aaaa的接收实体(也就是abc)是一个实参,在go语言中,实参其实就是将参数的值复制到函数里来(参数与函数调用前在内存里的地址是不一样的).bbbb和cccc的[接收实体]是一个形参,也就是说,函数调用前后参数所在内存地址是一样的!所以bbbb中,第一行的v还没赋值所以为0,第二行的v赋值2以后在cccc中打印v的值也为2.

​	这里还要提醒一句,对于goroutin(程道),切片,映射这三种类型来说,只有形参,而且不需要加*号.
另外,对于参数类型是[interface]的函数参数,只有实参,而且不会将interface结构所包含的地址复制!


###						Golang方法的接受者值与指针的区别###

##### 一、解决问题

​	假设有两个方法，一个方法的接收者是指针类型，一个方法的接收者是值类型，那么：
1. 对于值类型的变量和指针类型的变量，这两个方法有什么区别？
2. 如果这两个方法是为了实现一个接口，那么这两个方法都可以调用吗？
3. 如果方法是嵌入到其他结构体中的，那么上面两种情况又是怎样的？

#####二、实践分析

​	①、先声明一个结构体：	
```go
type T struct {
    Name string
}
func (t T) M1() {
    t.Name = "name1"
}
func (t *T) M2() {
    t.Name = "name2"
}
```

​	M1()的接受者是类型T,M2()的接受者是值类型*T,两个方法内都是改变Name值。下面声明一个T类型的变量，并调用M2()与M1()。
```go
t1 := T{"t1"}
fmt.Println("M1调用前：", t1.Name)
t1.M1()
fmt.Println("M1调用后：", t1.Name)
fmt.Println("M2调用前：", t1.Name)
t1.M2()
fmt.Println("M2调用后：", t1.Name)
```
​	输出结果为：
```go
M1调用前： t1
M1调用后： t1
M2调用前： t1
M2调用后： name2
```
​	下面猜测一下go会怎么处理。首先，接受者可以看做是函数的第一个参数，即这样的：func M1(t T),func M2(t *T)。go不是面对对象的言语，所有用那种看起来像面对对象的语法来理解可能有偏差。
​	当调用t1.M1() =》M1(t1)，实参和形参都是类型T,可以接受。此时在M1()中t只是t1的值拷贝，所以M1()的修改影响不到t1。
​	当调用t1.M2() =》 M2(t1)，这是将T类型传给了*T类型，go可能会取t1的地址传进去：M2(&t1)。所以M2()的修改可以影响到t1。
​	T类型的变量这两个方法都是拥有的。下面申明一个*T类型的变量，并调用M1()和M2()。
```go
t2 := &T{"t2"}
fmt.Println("M1调用前：", t2.Name)
t2.M1()
fmt.Println("M1调用后：", t2.Name)
fmt.Println("M2调用前：", t2.Name)
t2.M2()
fmt.Println("M2调用后：", t2.Name)
```

​	输出的结果为:
```go
M1调用前： t2
M1调用后： t2
M2调用前： t2
M2调用后： name2
```

​	t2.M1()=》M1(t2),t2是指针类型，取t2的值并拷贝一份传给M1。
​	t2.M2()=》M2(t2),都是指针类型，不需要转换。
​	*T类型的变量也是拥有这两个方法的。

​	②、传给接口
​	先声明一个接口

```go
type Intf interface {
  M1()
  M2()
}
```

​	使用:
```go
var t1 T = T{"t1"}
t1.M1()
t1.M2()
var t2 Intf = t1
t2.M1()
t2.M2()
```

​	报错:
```go
./main.go:9: cannot use t1 (type T) as type Intf in assignment:
    T does not implement Intf (M2 method has pointer receiver)
```

​	var t2 Intf = t1 这一行报错。
​	t1 是有 M2() 方法的，但是为什么传给 t2 时传不过去呢？
​	t1 是值类型，赋值给 t2 时是复制值而不是指针，假设 t1 可以赋值给 t2， t2.M2() 修改 Name 的值时也是修改的拷贝的		变量，无法影响到 t1，那把 t1 赋值给 t2 还有什么意义呢？所以这种赋值是不被允许的。
​	当把 var t2 Intf = t1 修改为 var t2 Intf = &t1 时编译通过，此时 t2 获得的是 t1 的地址， t2.M2() 的修改可以影响到 t1 了。如果声明一个方法 func f(t Intf) , 参数的传递和上面的直接赋值是一样的情况。				

​	③、嵌套类型

​	声明一个类型S,将T嵌入进去

```go
type S struct {
    T
}
```

​	使用下面的例子测试一下：

```go
t1 := T{"t1"}
s := S{t1}
fmt.Println("M1调用前：", s.Name)
s.M1()
fmt.Println("M1调用后：", s.Name)
fmt.Println("M2调用前：", s.Name)
s.M2()
fmt.Println("M2调用后：", s.Name)
fmt.Println(t1.Name)
```

​	输出结果：

```go
M1调用前： t1
M1调用后： t1
M2调用前： t1
M2调用后： name2
t1
```

​	将 T 嵌入 S， 那么 T 拥有的方法和属性 S 也是拥有的，但是接收者却不是 S 而是 T。
所以 s.M1() 相当于 M1(t1) 而不是 M1(s)。
最后 t1 的值没有改变，因为我们嵌入的是 T 类型，所以 S{t1} 的时候是将 t1 拷贝了一份。
假如我们将 s 赋值给 Intf 接口会怎么样呢？
```
    var intf Intf = s
    intf.M1()
    intf.M2()
```

​	报错：

```
cannot use s (type S) as type Intf in assignment:
    S does not implement Intf (M2 method has pointer receiver)
```

还是 M2() 的问题，因为 s 此时还是值类型。
var intf Intf = &s 这样的话编译通过了，如果在 intf.M2()中改变了 Name 的值， s.Name被改变了，但是 t1.Name依然没变，因为现在 t1 和 s 已经没有联系了。

------

​	④、嵌入 *T 试试：
```
type S struct {
    *T
}
```

​	使用时这样：
```
    t1 := T{"t1"}
    s := S{&t1}

    fmt.Println("M1调用前：", s.Name)
    s.M1()
    fmt.Println("M1调用后：", s.Name)

    fmt.Println("M2调用前：", s.Name)
    s.M2()
    fmt.Println("M2调用后：", s.Name)

    fmt.Println(t1.Name)
```

​	惟一的区别是最后 t1 的值变了，因为我们复制的是指针。接着赋值给接口试试：
```
    var intf Intf = s
    intf.M1()
    intf.M2()
    fmt.Println(s.Name)
```

​	编译没有报错。这里我们传递给 intf 的是值类型而不是指针，为什么可以通过呢？
​	拷贝 s 的时候里面的 T 是指针类型，所以调用 M2() 的时候传递进去的是一个指针。
​	var intf Intf = &s的效果和上面一样。



###				Golang日志库glog介绍####

#####一、简介

​	[glog](https://github.com/golang/glog)是Google开源日志库C++ glog的Go语言精简版,很小才两个文件。
​	它通过在命令行设置标志参数的方式来控制日志记录行为,线程安全,atomic操作,性能很高.支持分级(INFO/WARNING/ERROR/FATAL)。可以设置按级别或条件来记录日志,可以查看指定行的运行时堆栈信息。

#####二、参数说明

​	支持参数

```go
flag.String("log_dir", "", "If non-empty, write log files in this directory")
//日志输出目录 

flag.BoolVar(&logging.toStderr, "logtostderr", false, "log to standard error instead of files")
//logtostderr值为true的时候，日志信息输出到stderr。默认值为 false。

flag.BoolVar(&logging.alsoToStderr, "alsologtostderr", false, "log to standard error as well as files")
//alsologtostderr值为true的时候，日志信息同时输出到stderr及文件。默认值为 false。

flag.Var(&logging.verbosity, "v", "log level for V logs")
//输出小于等于此级别的日志信息，大于此级别的不会输出 

flag.Var(&logging.stderrThreshold, "stderrthreshold", "logs at or above this threshold go to stderr")
//严重性级别在指定级别以上的日志信息除了写入日志文件以外，还会输出到stderr。

flag.Var(&logging.vmodule, "vmodule", "comma-separated list of pattern=N settings for file-filtered logging")
//会输出匹配表达式的.go文件中，级别小于等于N的日志信息.不匹配表达式的文件则只会输出默认日志信息。但注意,此选项可以被-v 参数覆盖

flag.Var(&logging.traceLocation, "log_backtrace_at", "when logging hits line file:N, emit a stack trace")
//指定行的运行时堆栈信息
```

#####四、使用注意
１．因为是使用时依参数配置，所以需在main()中加上flag.Parse()
２．需在结尾加上glog.Flush()
３．依级别生成不同的日志文件，但级别高的日志信息会同时在级别低的日志文件中输出

#####三、实例讲解

```go
package main

import (
	"flag"
	"glog"
	"os"
	"test"
)

func main() {
	flag.Parse()
	p, err := os.Getwd()
	if err != nil {
		glog.Info("Getwd: ", err)
	} else {
		glog.Info("Getwd: ", p)
	}
	glog.Info("Prepare to repel boarders")
	glog.Info("222222222222---log_backtrace_at")
	glog.Info("333333333333")
	glog.V(1).Infoln("Processed1", "nItems1", "elements1")
	glog.V(2).Infoln("Processed2", "nItems2", "elements2")
	glog.V(3).Infoln("Processed3", "nItems3", "elements3")
	glog.V(4).Infoln("Processed4", "nItems4", "elements4")
	glog.V(5).Infoln("Processed5", "nItems5", "elements5")
	glog.Error("errrrr")

	/*
		if glog.V(2) {
			glog.Info("Starting transaction...")
		}
		glog.V(2).Infoln("Processed", "nItems", "elements")

		ch := make(chan int)
		go func() {
			for i := 0; i < 100; i++ {
				glog.Info("info:", i)
			}
			ch <- 1
		}()
		<-ch
		glog.Fatalf("Initialization failed: %s", errors.New("test info"))

	*/
	test.TestVmodule()
	exit()
}

func exit() {
	glog.Flush()
}
```

###					Golang-http源码分析###

Go对web服务器的编写提供了非常好的支持，标准库中提供了net/http包来方便编写服务器。用http包写一个最简单的hello world服务器，代码如下：
```go
package main
import "net/http"
func main() {
	http.HandleFunc("/",func(w http.ResponseWriter, r *http.Request){
      		w.Write([]byte(`hello world`))
	})
	http.ListenAndServe(":8080",nil)
}
```
上述代码极其简单，却能够圆满完成响应工作，我们将分析http.ListenAndServe().
首先，http.ListenAndServe源码位置在$GOPATH/src/pkg/net/http/server.go文件中打开它会发现这页代码非常长，我们查找到http.ListenAndServe的func：
```go
func ListenAndServe(addr string, handler Handler) error {
	server := &Server{Addr: addr, Handler: handler}
	return server.ListenAndServe()
}
```
从这个函数中就可以看出，调用http.ListenAndServe之后真正起作用的是Server结构体LisntenAndServe方法，给http.ListenAndServe传递的参数只是用来创建一个Server结构体实例，Server结构体的定义如下：
```go
type Server struct {
	Addr           string        // 服务器的IP地址和端口信息
	Handler        Handler       // 请求处理函数的路由复用器
	ReadTimeout    time.Duration 
	WriteTimeout   time.Duration
	MaxHeaderBytes int       
	TLSConfig      *tls.Config  
	TLSNextProto map[string]func(*Server, *tls.Conn, Handler)
	ConnState func(net.Conn, ConnState)
	ErrorLog *log.Logger
	disableKeepAlives int32 
}
```
如果我们不传具体的参数给http.ListenAndServe，那么它会自动以":http"(等价于":80")和DefaulServeMux作为参数来创建Server结构体实例。接下来继续看看

```go
func (srv *Server) ListenAndServe() error {
	addr := srv.Addr
	if addr == "" {
		addr = ":http"  
		// 如果不指定服务器地址信息，默认以":http"作为地址信息
	}
	ln, err := net.Listen("tcp", addr)    
	// 这里创建了一个TCP Listener，之后用于接收客户端的连接请求
	if err != nil {
		return err
	}
	return srv.Serve(tcpKeepAliveListener{ln.(*net.TCPListener)})  
	// 调用Server.Serve()函数并返回
}
```

可以看到，Server.ListenAndServe中创建了一个服务器Listener，然后在返回时把它传给了Server.Serve()方法并调用Server.Serve()。
Server.Serve定义如下：
```go
func (srv *Server) Serve(l net.Listener) error {
	defer l.Close()
	var tempDelay time.Duration 
	// 这个循环就是服务器的主循环了，通过传进来的listener接收来自客户端的请求并建立连接，
	// 然后为每一个连接创建routine执行c.serve()，这个c.serve就是具体的服务处理了
	for {
		rw, e := l.Accept()
		if e != nil {
			if ne, ok := e.(net.Error); ok && ne.Temporary() {
				if tempDelay == 0 {
					tempDelay = 5 * time.Millisecond
				} else {
					tempDelay *= 2
				}
				if max := 1 * time.Second; tempDelay > max {
					tempDelay = max
				}
				srv.logf("http: Accept error: %v; retrying in %v", e, tempDelay)
				time.Sleep(tempDelay)
				continue
			}
			return e
		}
		tempDelay = 0
		c, err := srv.newConn(rw)
		if err != nil {
			continue
		}
		c.setState(c.rwc, StateNew) // before Serve can return
		go c.serve() 
		//这里为每一个建立的连接创建routine之后进行服务
	}
}
```

最后我们得出http.ListenAndServe的工作流程如下图

 ![listenandserve](/home/zounengren/图片/listenandserve.png)

 ###					Go channel使用原则###

​	管道是并发安全的队列，用于在Go的轻量级线程(Go协程)之间安全地传递消息。总的来讲，这些原语是Go语言中最为称道的特色功能之一。这种消息传递范式使得开发者可以以易于理解的语义和控制流来协调管理多线程并发任务，而这胜过使用回调函数或者共享内存。

​	即使管道如此强大，在公有的API中却不常见。在145个包中有超过6000个公有的API。在这上千个API中，去重后，只有５个用到了管道。

​	在公有的API中使用管道时，如何折衷考虑和取舍，缺乏指导。“共有API”，我是指“任何实现者和使用者是不同的两个人的编程接口”。这篇文章会深入讲解，为如何在共有API中使用管道，提供一系列的原则和解释。一些特例会在本章末尾讨论。 

​	①、API应该申明channel的方向性

​	time.After
​	`func After(d Duration)<-chan Time`
​	signal.Notify
​	`func Notify(c chan<-os.Signal,sig ...os.Signal)`

​	尽管并不常用，Go允许指定一个管道的方向性。语言规范这么写：

​	可选的<-操作符指定了管道的方向，发送或接收。如果没有指定方向，那么管道就是双向的,关键在于API签名中的方向操作符会被编译器强制检查。

​	②、向一个管道发送无上限数据流时文档需明确说明消费者在无法及时消费时的API行为

​	time.NewTicker

```go
// NewTicker returns a new Ticker containing a channel that will send the
// time with a period specified by the duration argument.
// It adjusts the intervals or drops ticks to make up for slow receivers.
// ...
func NewTicker(d Duration) *Ticker {
    ...
}
```

​	signal.Notify

```go
// Notify causes package signal to relay incoming signals to c.
// ...
// Package signal will not block sending to c
// ...
 
func Notify(c chan&lt;- os.Signal, sig ...os.Signal) {
```

​	ssh.Conn.OpenChannel

```go
// OpenChannel tries to open an channel.
// ...
// On success it returns the SSH Channel and a Go channel for
// incoming, out-of-band requests. The Go channel must be serviced, or
// the connection will hang.
 
OpenChannel(name string, data []byte) (Channel, &lt;-chan *Request, error)
```

​	当一个API向一个管道发送无界数据流时，在实现API时面临的问题是如果向管道发送数据会阻塞怎么办。阻塞的原因可能是管道已经满了或者管道是无缓冲的，没有go协程准备好接收数据。针对不同的场景要选择合适的行为，但是每个场景必须作出选择。例如，ssh包选择了阻塞，并且文档写明如果你不接受数据，连接就会被卡住。signal.Notify 和 time.Tick选择不阻塞，直接丢弃数据。

​	不足的是，Go本身并没有从类型或函数签名角度提供方法指定默认行为。作为API的设计者，你必须在文档中写明行为，不然其行为就是不定的。然而，多数情况下我们都是API的使用者而不是设计者，所以我们可以反过来记这个原则，反过来就是一条警告信息：

​	对于通过一个管道向一个慢速的消费者发送无界数据的API，在没有通读API的文档或者实现源码之前，你*不能*确定API的行为。

​	③、管道接受有限数据，且管道是作为参数传入时，需要用文档说明对于消费不及时的行为

​	反例如下

​	rpc.Client.Go

```go
func (client *Client) Go(serviceMethod string,
                         args interface{},
                         reply interface{},
                         done chan *Call
                         ) *Call
```

​	这个原则和第二个原则类似，不同点在于这个原则用于发送有界数据的API。不幸的是，在标准库中没有很好的例子。标准库中唯一的API就是rpc.Client.Go，但它违背了我们的原则。文档上这么写：

​	Go异步的调用这个函数。它会返回代表着调用的Call数据结构。在调用完成时，done管道会通过返回同一个Call对象来触发。如果done是空的，Go会分配一个新的管道；如果不空，done必须是有缓冲的，不然Go就会崩溃。

​	Go发送了有界数据（只有1，当远程调用结束时）。但是注意到，由于管道是被当作参数传递到函数中的，所以它仍然存在慢速消费者问题。即使你必须传一个带缓冲的管道进来，如果管道已满，向这个管道发送数据仍然可能会阻塞。*文档并没有定义这种场景下的行为*。需要我们来读读源码了：

```go
func (call *Call) done() {
     select {
     case call.Done &lt;- call:
     // ok
     default:
     	// We don not want to block here.  It is the caller&#039;s responsibility to 		// make sure the channel has enough buffer space. See comment in Go().
         if debugLog {
             log.Println("rpc: discarding Call reply due to insufficient Done chan capacity")
         }
     }
}
```

​	④、向一个管道发送无上限数据流的APIy应该接受管道作为参数，而不是返回一个新的管道

​	signal.Notify

​	`func Notify(c chan<.os.Signal, sig ...os.Signal)`

​	ssh.NewClient

​	`func NewClient(c Conn,chans <-NewChannel,reqs <-chan*Request *Client)`

​	当我第一次看到signal.Notify这个API时，我很疑惑，“为什么它接收一个管道作为输入而不是直接返回一个管道给我用？”“使用这个API需要调用方分配一个管道，难道API就不能替我们做么，像下面这样？”

​	`func Notify(sig ...os.Signal) <-chan os.Signal`

​	文档帮助我们理解为什么这不是好的选择：

​	signal包向c发送数据时并不会阻塞：调用方必须保证c有足够的缓冲空间来跟得上潜在的信号速度signal.Notify接收管道作为参数，因为它把缓冲空间的控制权交给了调用方。这使得调用方可以选择，在处理一个信号时，可以安全的忽略多少信号，这需要和缓存这些信号的内存开销作折衷考虑。

​	缓冲大小的控制在高吞吐系统中尤为重要。设想一个高吞吐的发布订阅系统的这样一个接口：

​	`func Subscribe(topic string, msgs chan<- Msg)`

​	往管道中发送越多的消息，管道同步称为性能瓶颈的可能性越大。由于API允许调用方创建管道，调用方需要考虑缓冲，进而性能可以由调用方控制。这是一种更灵活的设计。

​	如果仅仅是控制缓冲的大小，我们可能会争论如下的API就足够了：

​	`func Notify(sig ...os.Signal, bufSize int <-chan os.Signal )`

​	这样设计，管道作为参数还是必须的，因为这样允许调用方使用一个管道动态的处理不同类型的信号。这样设计为调用方提供了更多的程序结构和性能上的灵活性。作为一个假想实验，让我们用Subscribe API来构建需求。订阅newcustomer管道，并对于每一条消息，为消费者订阅其主题。如果API允许我们传递接收管道，我们可以这样写：
```go
msgs := make(chan Msg, 128)
Subscribe("newcustomer", msgs)
for m := range msgs {
    switch m.Topic {
    case "newcustomer":
        Subscribe(msg.Payload, msgs)
    default:
        handleCustomerMessage(m)
}
```

​	但是，如果管道被返回了，调用方不得不为每一个订阅启动一个单独的go协程。这在任何复用场景都会带来额外的内存和同步开销:

```go
for m := range Subscribe("newcustomer") {
    go subCustomer(m.Payload)
}
func subCustomer(topic string) {
    for m := range Subscribe(topic) {
        handleCustomerMessage(m)
    }
}
```

​	⑤、发送有限数据的API可以通过返回一个适合大小缓冲的管道达到目的

​	http.CloseNotifier

```go
type CloseNotifier interface {
        // CloseNotify returns a channel that receives a single value
        // when the client connection has gone away.
        CloseNotify() &lt;-chan bool
}
```

​	time.After

```go
func After(d Duration) <-chan Time
```

​	当API向一个管道发送有界数据时，可以返回一个拥有容纳全部数据的缓冲空间的管道。这个要返回的管道的方向性标识保证了调用方必须遵守约定。CloseNotify 和After返回的管道 都利用了这一点。

​	同时，需要注意到，通过允许调用方传递一个管道来接收数据，这些调用可能会更灵活。但需要处理当管道满了的时候（原则3）。例如，另外一个可选的，更灵活的CloseNotifier：

```go
type CloseNotifier interface {
        // CloseNotify sends a single value with the ResponseWriter whose
        // underlying connection has gone away.
        CloseNotify(chan<- http.ResponseWriter)
}
```

​	但是这种额外的灵活性带来的开销并不值得关注，因为单一的调用方很少会同时等待多个关闭通知。毕竟，关闭通知只有在某个连接上下文内才有效。不同的连接一般都是相互独立的。

​	⑥、特例

​	rpc.Client.Go

​	传过来的done管道没有方向性标识符：

```go
func (client *Client) Go(serviceMethod string,
                         args interface{},
                         reply interface{},
                         done chan *Call
                         ) *Call
```

​	直观上来说，这样是因为channel done是作为Call结构体的一部分返回的。

```go
type Call struct {
        // ...
        Done          chan *Call  // Strobes when call is complete.
}
```

​	这种灵活性是必须的，这样允许你传nil分配一个channel 出来。如果坚持①，则需要从Call结构提中取出done并且声明两个函数：

```go
func (c *Client) Go(method string,
                    args interface{},
                    reply interface{}
                    ) (*Call,<-chan *Call)
 
func (c *Client) GoEx(method string,
                      args interface{},
                      reply interface{},
                      done chan&lt;- *Call
                      ) *Call
```

​	go.crypto/ssh

```go
func NewClientConn(c net.Conn, addr string, config *ClientConfig)
    (Conn, <-chan NewChannel, &lt;-chan *Request, error)
```

​	time.Tick

```go
func Tick(d Duration) <-chan Time
```

​	go.crypto/ssh包几乎在所有的地方返回了无界的数据流管道。ssh.NewClientConn只是其中的一个。给调用者更多控制权和灵活性的API应该是这样的：

```go
func NewClientConn(c net.Conn,
                   addr string,
                   config *ClientConfig,
                   channels chan<- NewChannel,
                   reqs chan<- *Request
                   ) (Conn, error)
```

​	time.Tick也违反了这个原则，但是易于理解。我们很少会创建非常多的计时器，通常都是独立的处理不同的计时器。这个例子中缓冲也没太大意义。





​	

  








