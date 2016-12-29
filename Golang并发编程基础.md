### Golang并发编程基础

参考资料：

１、[Go并发编程基础](http://blog.xiayf.cn/2015/05/20/fundamentals-of-concurrent-programming/)

原文：[Fundamentals of concurrent programming](http://www.nada.kth.se/~snilsson/concurrency/)

译者：[youngsterxyf](https://github.com/youngsterxyf)

本文是一篇并发编程方面的入门文章，以[Go语言](http://golang.org/)编写示例代码，内容涵盖：

- 运行期并发线程（goroutines）
- 基本的同步技术（管道和锁）
- Go语言中基本的并发模式
- 死锁和数据竞争
- 并行计算

在开始阅读本文之前，你应该知道如何编写简单的Go程序。如果你熟悉的是C/C++、Java或Python之类的语言，那么 [Go语言之旅](http://tour.golang.org/welcome/1) 能提供所有必要的背景知识。也许你还有兴趣读一读 [为C++程序员准备的Go语言教程](http://code.google.com/p/go-wiki/wiki/GoForCPPProgrammers) 或 [为Java程序员准备的Go语言教程](http://www.nada.kth.se/~snilsson/go_for_java_programmers/)。

#### 1. 运行期线程

Go允许使用`go`语句开启一个新的运行期线程，即 [goroutine](http://golang.org/ref/spec#Go_statements)，以一个不同的、新创建的goroutine来执行一个函数。同一个程序中的所有goroutine共享同一个地址空间。

Goroutine非常轻量，除了为之分配的栈空间，其所占用的内存空间微乎其微。并且其栈空间在开始时非常小，之后随着堆存储空间的按需分配或释放而变化。内部实现上，goroutine会在多个操作系统线程上多路复用。如果一个goroutine阻塞了一个操作系统线程，例如：等待输入，这个线程上的其他goroutine就会迁移到其他线程，这样能继续运行。开发者并不需要关心/担心这些细节。

下面所示程序会输出“Hello from main goroutine”。也可能会输出“Hello from another goroutine”，具体依赖于两个goroutine哪个先结束。

```
func main() {
    go fmt.Println("Hello from another goroutine")
    fmt.Println("Hello from main goroutine")

    // 至此，程序运行结束，
    // 所有活跃的goroutine被杀死
}

```

[goroutine1.go](http://www.nada.kth.se/~snilsson/concurrency/src/goroutine1.go)

接下来的这个程序，多数情况下，会输出“Hello from main goroutine”和“Hello from another goroutine”，输出的顺序不确定。但还有另一个可能性是：第二个goroutine运行得极其慢，在程序结束之前都没来得及输出相应的消息。

```
func main() {
    go fmt.Println("Hello from another goroutine")
    fmt.Println("Hello from main goroutine")

    time.Sleep(time.Second)        // 等待1秒，等另一个goroutine结束
}

```

[goroutine2.go](http://www.nada.kth.se/~snilsson/concurrency/src/goroutine2.go)

下面则是一个相对更加实际的示例，其中定义了一个函数使用并发来推迟触发一个事件。

```
// 函数Publish在给定时间过期后打印text字符串到标准输出
   // 该函数并不会阻塞而是立即返回
func Publish(text string, delay time.Duration) {
    go func() {
        time.Sleep(delay)
        fmt.Println("BREAKING NEWS:", text)
    }()    // 注意这里的括号。必须调用匿名函数
}

```

[publish1.go](http://www.nada.kth.se/~snilsson/concurrency/src/publish1.go)

你可能会这样使用`Publish`函数：

```
func main() {
    Publish("A goroutine starts a new thread of execution.", 5*time.Second)
    fmt.Println("Let’s hope the news will published before I leave.")

    // 等待发布新闻
    time.Sleep(10 * time.Second)

    fmt.Println("Ten seconds later: I’m leaving now.")
}

```

[publish1.go](http://www.nada.kth.se/~snilsson/concurrency/src/publish1.go)

这个程序，绝大多数情况下，会输出以下三行，顺序固定，每行输出之间相隔5秒。

```
$ go run publish1.go
Let’s hope the news will published before I leave.
BREAKING NEWS: A goroutine starts a new thread of execution.
Ten seconds later: I’m leaving now.

```

一般来说，通过睡眠的方式来编排线程之间相互等待是不太可能的。下一章节会介绍Go语言中的一种同步机制 - 管道，并演示如何使用管道让一个goroutine等待另一个goroutine。

#### 2. 管道（channel）

![Sushi conveyor belt](http://www.nada.kth.se/~snilsson/concurrency/sushi-conveyor-belt.jpg)

[管道](http://golang.org/ref/spec#Channel_types)是Go语言的一个构件，提供一种机制用于两个goroutine之间通过传递一个指定类型的值来同步运行和通讯。操作符`<-`用于指定管道的方向，发送或接收。如果未指定方向，则为双向管道。

```
chan Sushi        // 可用来发送和接收Sushi类型的值
chan<- float64    // 仅可用来发送float64类型的值
<-chan int        // 仅可用来接收int类型的值

```

管道是引用类型，基于make函数来分配。

```
ic := make(chan int)    // 不带缓冲的int类型管道
wc := make(chan *Work, 10)    // 带缓冲的Work类型指针管道

```

如果通过管道发送一个值，则将`<-`作为二元操作符使用。通过管道接收一个值，则将其作为一元操作符使用：

```
ic <- 3        // 往管道发送3
work := <-wc    // 从管道接收一个指向Work类型值的指针

```

如果管道不带缓冲，发送方会阻塞直到接收方从管道中接收了值。如果管道带缓冲，发送方则会阻塞直到发送的值被拷贝到缓冲区内；如果缓冲区已满，则意味着需要等待直到某个接收方获取到一个值。接收方在有值可以接收之前会一直阻塞。

**关闭管道（Close）**

[close](http://golang.org/ref/spec#Close) 函数标志着不会再往某个管道发送值。在调用`close`之后，并且在之前发送的值都被接收后，接收操作会返回一个零值，不会阻塞。一个多返回值的接收操作会额外返回一个布尔值用来指示返回的值是否发送操作传递的。

```
ch := make(chan string)
go func() {
    ch <- "Hello!"
    close(ch)
}()
fmt.Println(<-ch)    // 输出字符串"Hello!"
fmt.Println(<-ch)    // 输出零值 - 空字符串""，不会阻塞
fmt.Println(<-ch)    // 再次打印输出空字符串""
v, ok := <-ch        // 变量v的值为空字符串""，变量ok的值为false

```

一个带有`range`子句的`for`语句会依次读取发往管道的值，直到该管道关闭：

```
func main() {
    // 译注：要想运行该示例，需要先定义类型Sushi，如type Sushi string
    var ch <-chan Sushi = Producer()
    for s := range ch {
        fmt.Println("Consumed", s)
    }
}

func Producer() <-chan Sushi {
    ch := make(chan Sushi)
    go func(){
        ch <- Sushi("海老握り")    // Ebi nigiri
        ch <- Sushi("鮪とろ握り") // Toro nigiri
        close(ch)
    }()
    return ch
}

```

[sushi.go](http://www.nada.kth.se/~snilsson/concurrency/src/sushi.go)

#### 3. 同步

下一个示例中，我们让`Publish`函数返回一个管道 - 用于在发布text变量值时广播一条消息：

```
// 在给定时间过期时，Publish函数会打印text变量值到标准输出
// 在text变量值发布后，该函数会关闭管道wait
func Publish(text string, delay time.Duration) (wait <-chan struct{}) {
    ch := make(chan struct{})
    go func() {
        time.Sleep(delay)
        fmt.Println("BREAKING NEWS:", text)
        close(ch)    // 广播 - 一个关闭的管道都会发送一个零值
    }()
    return ch
}

```

[publish2.go](http://www.nada.kth.se/~snilsson/concurrency/src/publish2.go)

注意：我们使用了一个空结构体的管道：`struct{}`。这明确地指明该管道仅用于发信号，而不是传递数据。

我们可能会这样使用这个函数：

```
func main() {
    wait := Publish("Channels let goroutines communicate.", 5*time.Second)
    fmt.Println("Waiting for the news...")
    <-wait
    fmt.Println("The news is out, time to leave.")
}

```

[publish2.go](http://www.nada.kth.se/~snilsson/concurrency/src/publish2.go)

这个程序会按指定的顺序输出以下三行内容。最后一行在新闻（news）一出就会立即输出。

```
$ go run publish2.go
Waiting for the news...
BREAKING NEWS: Channels let goroutines communicate.
The news is out, time to leave.

```

#### 4. 死锁

![traffic jam](http://www.nada.kth.se/~snilsson/concurrency/traffic-jam.jpg)

现在我们在`Publish`函数中引入一个bug：

```
func Publish(text string, delay time.Duration) (wait <-chan struct{}) {
    ch := make(chan struct{})
    go func() {
        time.Sleep(delay)
        fmt.Println("BREAKING NEWS:", text)
        // 译注：注意这里将close函数调用注释掉了
        //close(ch)
    }()
    return ch
}

```

主程序还是像之前一样开始运行：输出第一行，然后等待5秒，这时`Publish`函数开启的goroutine会输出突发新闻（breaking news），然后退出，留下主goroutine独自等待。

```
func main() {
    wait := Publish("Channels let goroutines communicate.", 5*time.Second)
    fmt.Println("Waiting for the news...")
    // 译注：注意下面这一句
    <-wait
    fmt.Println("The news is out, time to leave.")
}

```

此刻之后，程序无法再继续往下执行。众所周知，这种情形即为死锁。

> 死锁是线程之间相互等待，其中任何一个都无法向前运行的情形。

Go语言对于运行时的死锁检测具备良好的支持。当没有任何goroutine能够往前执行的情形发生时，Go程序通常会提供详细的错误信息。以下就是我们的问题程序的输出：

```
Waiting for the news...
BREAKING NEWS: Channels let goroutines communicate.
fatal error: all goroutines are asleep - deadlock!

goroutine 1 [chan receive]:
main.main()
    .../goroutineStop.go:11 +0xf6

goroutine 2 [syscall]:
created by runtime.main
    .../go/src/pkg/runtime/proc.c:225

goroutine 4 [timer goroutine (idle)]:
created by addtimer
    .../go/src/pkg/runtime/ztime_linux_amd64.c:73

```

大多数情况下找出Go程序中造成死锁的原因都比较容易，那么剩下的就是如何解决这个bug了。

#### 5. 数据竞争（data race）

死锁也许听起来令人挺忧伤的，但伴随并发编程真正灾难性的错误其实是数据竞争，相当常见，也可能非常难于调试。

> 当两个线程并发地访问同一个变量，并且其中至少一个访问是写操作时，数据竞争就发生了。

下面的这个函数就有数据竞争问题，其行为是未定义的。例如，可能输出数值1。代码之后是一个可能性解释，试图搞清楚这一切是如何发生得。

```
func race() {
    wait := make(chan struct{})
    n := 0
    go func() {
        // 译注：注意下面这一行
        n++ // 一次访问: 读, 递增, 写
        close(wait)
    }()
    // 译注：注意下面这一行
    n++ // 另一次冲突的访问
    <-wait
    fmt.Println(n) // 输出：未指定
}

```

[datarace.go](http://www.nada.kth.se/~snilsson/concurrency/src/datarace.go)

代码中的两个goroutine（假设命名为`g1`和`g2`）参与了一次竞争，我们无法获知操作会以何种顺序发生。以下是诸多可能中的一种：

- `g1` 从 `n` 中获取值0
- `g2` 从 `n` 中获取值0
- `g1` 将值从0增大到1
- `g1` 将1写到 `n`
- `g2` 将值从0增大到1
- `g2` 将1写到 `n`
- 程序输出 n 的值，当前为1

“数据竞争（data race）”这名字有点误导的嫌疑。不仅操作的顺序是未定义的，其实根本没有任何保证（no guarantees whatsoever）。编译器和硬件为了得到更好的性能，经常都会对代码进行上下内外的顺序变换。如果你看到一个线程处于中间行为状态时，那么当时的场景可能就像下图所示的一样：

![mid action](http://www.nada.kth.se/~snilsson/concurrency/mid-action.jpg)

避免数据竞争的唯一方式是线程间同步访问所有的共享可变数据。有几种方式能够实现这一目标。Go语言中，通常是使用管道或者锁。（[sync](http://golang.org/pkg/sync/)和[sync/atomic](http://golang.org/pkg/sync/atomic/)包中还有更低层次的机制可供使用，但本文中不做讨论）。

Go语言中，处理并发数据访问的推荐方式是使用管道从一个goroutine中往下一个goroutine传递实际的数据。有格言说得好：“不要通过共享内存来通讯，而是通过通讯来共享内存”。

```
func sharingIsCaring() {
    ch := make(chan int)
    go func() {
        n := 0 // 仅为一个goroutine可见的局部变量.
        n++
        ch <- n // 数据从一个goroutine离开...
    }()
    n := <-ch   // ...然后安全到达另一个goroutine.
    n++
    fmt.Println(n) // 输出: 2
}

```

[datarace.go](http://www.nada.kth.se/~snilsson/concurrency/src/datarace.go)

以上代码中的管道肩负双重责任 - 从一个goroutine将数据传递到另一个goroutine，并且起到同步的作用：发送方goroutine会等待另一个goroutine接收数据，接收方goroutine也会等待另一个goroutine发送数据。

[Go语言内存模型](http://golang.org/ref/mem) - 要保证一个goroutine中对一个变量的读操作得到的值正好是另一个goroutine中对同一个变量写操作产生的值，条件相当复杂，但goroutine之间只要通过管道来共享所有可变数据，那么就能远离数据竞争了。

#### 6. 互斥锁

![lock](http://www.nada.kth.se/~snilsson/concurrency/lock.jpg)

有时，通过显式加锁，而不是使用管道，来同步数据访问，可能更加便捷。Go语言标准库为这一目的提供了一个互斥锁 - [sync.Mutex](http://golang.org/pkg/sync/#Mutex)。

要想这类加锁起效的话，关键之处在于：所有对共享数据的访问，不管读写，仅当goroutine持有锁才能操作。一个goroutine出错就足以破坏掉一个程序，引入数据竞争。

因此，应该设计一个自定义数据结构，具备明确的API，确保所有的同步都在数据结构内部完成。下例中，我们构建了一个安全、易于使用的并发数据结构，`AtomicInt`，用于存储一个整型值。任意数量的goroutine都能通过`Add`和`Value`方法安全地访问这个数值。

```
// AtomicInt是一个并发数据结构，持有一个整数值
// 该数据结构的零值为0
type AtomicInt struct {
    mu sync.Mutex // 锁，一次仅能被一个goroutine持有。
    n  int
}

// Add方法作为一个原子操作将n加到AtomicInt
func (a *AtomicInt) Add(n int) {
    a.mu.Lock() // 等待锁释放，然后持有它
    a.n += n
    a.mu.Unlock() // 释放锁
}

// Value方法返回a的值
func (a *AtomicInt) Value() int {
    a.mu.Lock()
    n := a.n
    a.mu.Unlock()
    return n
}

func lockItUp() {
    wait := make(chan struct{})
    var n AtomicInt
    go func() {
        n.Add(1) // 一个访问
        close(wait)
    }()
    n.Add(1) // 另一个并发访问
    <-wait
    fmt.Println(n.Value()) // 输出: 2
}

```

[datarace.go](http://www.nada.kth.se/~snilsson/concurrency/src/datarace.go)

#### 7. 检测数据竞争

竞争有时非常难于检测。下例中的这个函数有一个数据竞争问题，执行这个程序时会输出`55555`。尝试一下，也许你会得到一个不同的结果。（[sync.WaitGroup](http://golang.org/pkg/sync/#WaitGroup)是Go语言标准库的一部分；用于等待一组goroutine结束运行。）

```
func race() {
    var wg sync.WaitGroup
    wg.Add(5)
    // 译注：注意下面这行代码中的i++
    for i := 0; i < 5; i++ {
        go func() {
            // 注意下一行代码会输出什么？为什么？
            fmt.Print(i) // 6个goroutine共享变量i
            wg.Done()
        }()
    }
    wg.Wait() // 等待所有（5个）goroutine运行结束
    fmt.Println()
}

```

[raceClosure.go](http://www.nada.kth.se/~snilsson/concurrency/src/raceClosure.go)

对于输出`55555`，一个貌似合理的解释是：执行`i++`的goroutine在其他goroutine执行打印语句之前就完成了5次`i++`操作。实际上变量`i`更新后的值为其他goroutine所见纯属巧合。

一个简单的解决方案是：使用一个局部变量，然后当开启新的goroutine时，将数值作为参数传递：

```
func correct() {
    var wg sync.WaitGroup
    wg.Add(5)
    for i := 0; i < 5; i++ {
        go func(n int) { // 使用局部变量
            fmt.Print(n)
            wg.Done()
        }(i)
    }
    wg.Wait()
    fmt.Println()
}

```

[raceClosure.go](http://www.nada.kth.se/~snilsson/concurrency/src/raceClosure.go)

这次代码就对了，程序会输出期望的结果，如：`24031`。注意：goroutine之间的运行顺序是不确定的。

仍旧使用闭包，但能够避免数据竞争也是可能的，必须小心翼翼地让每个goroutine使用一个独有的变量。

```
func alsoCorrect() {
    var wg sync.WaitGroup
    wg.Add(5)
    for i := 0; i < 5; i++ {
        n := i // 为每个闭包创建一个独有的变量
        go func() {
            fmt.Print(n)
            wg.Done()
        }()
    }
    wg.Wait()
    fmt.Println()
}

```

[raceClosure.go](http://www.nada.kth.se/~snilsson/concurrency/src/raceClosure.go)

**数据竞争自动检测**

一般来说，不太可能能够自动检测发现所有可能的数据竞争情况，但Go（从版本1.1开始）有一个强大的[数据竞争检测器](http://tip.golang.org/doc/articles/race_detector.html)。

这个工具用起来也很简单：只要在使用`go`命令时加上`-race`标记即可。开启检测器运行上面的程序会给出清晰且信息量大的输出：

```
$ go run -race raceClosure.go
Race:
==================
WARNING: DATA RACE
Read by goroutine 2:
    main.func·001()
      ../raceClosure.go:22 +0x65

Previous write by goroutine 0:
    main.race()
        ../raceClosure.go:20 +0x19b
    main.main()
        ../raceClosure.go:10 +0x29
    runtime.main()
        ../go/src/pkg/runtime/proc.c:248 +0x91

Goroutine 2 (running) created at:
    main.race()
      ../raceClosure.go:24 +0x18b
    main.main()
      ../raceClosure.go:10 +0x29
     runtime.main()
      ../go/src/pkg/runtime/proc.c:248 +0x91

==================
55555
Correct:
01234
Also correct:
01324
Found 1 data race(s)
exit status 66

```

该工具发现一处数据竞争，包含：一个goroutine在第20行对一个变量进行写操作，跟着另一个goroutine在第22行对同一个变量进行了未同步的读操作。

注意：竞争检测器只能发现在运行期确实发生的数据竞争（译注：我也不太理解这话，请指导）

#### 8. Select语句

[select语句](http://golang.org/ref/spec#Select_statements)是Go语言并发工具集中的终极工具。select用于从一组可能的通讯中选择一个进一步处理。如果任意一个通讯都可以进一步处理，则从中随机选择一个，执行对应的语句。否则，如果又没有默认分支（default case），select语句则会阻塞，直到其中一个通讯完成。

以下是一个玩具示例，演示`select`语句如何用于实现一个随机数生成器：

```
// RandomBits函数 返回一个管道，用于产生一个比特随机序列
func RandomBits() <-chan int {
    ch := make(chan int)
    go func() {
        for {
            select {
            case ch <- 0: // 注意：分支没有对应的处理语句
            case ch <- 1:
            }
        }
    }()
    return ch
}

```

[randBits.go](http://www.nada.kth.se/~snilsson/concurrency/src/randBits.go)

下面是相对更加实际一点的例子：如何使用select语句为一个操作设置一个时间限制。代码会输出变量news的值或者超时消息，具体依赖于两个接收语句哪个先执行：

```
select {
case news := <-NewsAgency:
    fmt.Println(news)
case <-time.After(time.Minute):
    fmt.Println("Time out: no news in one minute.")
}

```

函数 [time.After](http://golang.org/pkg/time/#After) 是Go语言标准库的一部分；它会在等待指定时间后将当前的时间发送到返回的管道中。

#### 9. 综合所有示例

![couples](http://www.nada.kth.se/~snilsson/concurrency/couples.jpg)

花点时间认真研究一下这个示例。如果你完全理解，也就对Go语言中并发的应用方式有了全面的掌握。

这个程序演示了如何将管道用于被任意数量的goroutine发送和接收数据，也演示了如何将select语句用于从多个通讯中选择一个。

```
func main() {
    people := []string{"Anna", "Bob", "Cody", "Dave", "Eva"}
    match := make(chan string, 1) // 为一个未匹配的发送操作提供空间
    wg := new(sync.WaitGroup)
    wg.Add(len(people))
    for _, name := range people {
        go Seek(name, match, wg)
    }
    wg.Wait()
    select {
    case name := <-match:
        fmt.Printf("No one received %s’s message.\n", name)
    default:
        // 没有待处理的发送操作
    }
}

// 函数Seek 发送一个name到match管道或从match管道接收一个peer，结束时通知wait group
func Seek(name string, match chan string, wg *sync.WaitGroup) {
    select {
    case peer := <-match:
        fmt.Printf("%s sent a message to %s.\n", peer, name)
    case match <- name:
        // 等待某个goroutine接收我的消息
    }
    wg.Done()
}

```

[matching.go](http://www.nada.kth.se/~snilsson/concurrency/src/matching.go)

示例输出：

```
$ go run matching.go
Cody sent a message to Bob.
Anna sent a message to Eva.
No one received Dave’s message.

```

#### 10. 并行计算

![CPUs](http://www.nada.kth.se/~snilsson/concurrency/cpus.jpg)

并发的一个应用是将一个大的计算切分成一些工作单元，调度到不同的CPU上同时地计算。

将计算分布到多个CPU上更多是一门艺术，而不是一门科学。以下是一些经验法则：

- 每个工作单元应该花费大约100微秒到1毫秒的时间用于计算。如果单元粒度太小，切分问题以及调度子问题的管理开销可能就会太大。如果单元粒度太大，整个计算也许不得不等待一个慢的工作项结束。这种缓慢可能因为多种原因而产生，比如：调度、其他进程的中断或者糟糕的内存布局。（注意：工作单元的数目是不依赖于CPU的数目的）
- 尽可能减小共享的数据量。并发写操作的代价非常大，特别是如果goroutine运行在不同的CPU上。读操作之间的数据共享则通常不会是个问题。
- 数据访问尽量利用良好的局部性。如果数据能保持在缓存中，数据加载和存储将会快得多得多，这对于写操作也格外地重要。

下面的这个示例展示如何切分一个开销很大的计算并将其分布在所有可用的CPU上进行计算。先看一下有待优化的代码：

```
type Vector []float64

// 函数Convolve 计算 w = u * v，其中 w[k] = Σ u[i]*v[j], i + j = k
// 先决条件：len(u) > 0, len(v) > 0
func Convolve(u, v Vector) (w Vector) {
    n := len(u) + len(v) - 1
    w = make(Vector, n)

    for k := 0; k < n; k++ {
        w[k] = mul(u, v, k)
    }
    return
}

// 函数mul 返回 Σ u[i]*v[j], i + j = k.
func mul(u, v Vector, k int) (res float64) {
    n := min(k+1, len(u))
    j := min(k, len(v)-1)
    for i := k - j; i < n; i, j = i+1, j-1 {
        res += u[i] * v[j]
    }
    return
}

```

思路很简单：确定合适大小的工作单元，然后在不同的goroutine中执行每个工作单元。以下是并发版本的 `Convolve`：

```
func Convolve(u, v Vector) (w Vector) {
    n := len(u) + len(v) - 1
    w = make(Vector, n)

    // 将 w 切分成花费 ~100μs-1ms 用于计算的工作单元
    size := max(1, 1<<20/n)

    wg := new(sync.WaitGroup)
    wg.Add(1 + (n-1)/size)
    for i := 0; i < n && i >= 0; i += size { // 整型溢出后 i < 0
        j := i + size
        if j > n || j < 0 { // 整型溢出后 j < 0
            j = n
        }

        // 这些goroutine共享内存，但是只读
        go func(i, j int) {
            for k := i; k < j; k++ {
                w[k] = mul(u, v, k)
            }
            wg.Done()
        }(i, j)
    }
    wg.Wait()
    return
}

```

[convolution.go](http://www.nada.kth.se/~snilsson/concurrency/src/convolution.go)

工作单元定义之后，通常情况下最好将调度工作交给运行时和操作系统。然而，对于Go 1.* 你也许需要告诉运行时希望多少个goroutine来同时地运行代码。

```
func init() {
    numcpu := runtime.NumCPU()
    runtime.GOMAXPROCS(numcpu) // 尝试使用所有可用的CPU
}

```

