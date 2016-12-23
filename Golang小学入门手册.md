###					一、Golang入门###

##### 1.1 Golang简介
​	Go是Google开发的一种编译型，可并行化，并具有垃圾回收功能的编程语言。
​	罗布·派克（Rob Pike）,罗伯特·格瑞史莫（Robert Griesemer），及肯·汤普逊于2007年9月开始设计Go语言，随后Ian Lance Taylor, Russ Cox加入项目中。Go语言是基于Inferno操作系统所开发的。Go语言于2009年11月正式宣布推出，成为开放源代码项目，并在Linux及Mac OS X平台上进行了实现，后追加Windows系统下的实现。
​	2015年，Golang的受欢迎程度急剧上升，随着一大批PaaS开源工具的兴起与流行，诸如docker、kubernetes等，开源社区的发展促进Golang的流行，Golang的高效等特性同样为开源软件的性能提供保障，两者相辅相成。

##### 1.2 包、变量和函数

​	1.2.1 包

​	每个 Go 程序都是由包组成的。
​	程序运行的入口是包 `main` 。
​	这个程序使用并导入了包 `"fmt"` 和 `"math/rand"` 。
​	按照惯例，包名与导入路径的最后一个目录一致。例如，`"math/rand"` 包由 `package rand` 语句开始。
​	**注意**：这个程序的运行环境是确定性的，因此 `rand.Intn` 每次都会返回相同的数字。 （为了得到不同的随机数，需要提供一个随机数种子，参阅 [`rand.Seed`](https://go-zh.org/pkg/math/rand/#Seed)。）

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

​	**常量**

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

​	











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



​	

​    








