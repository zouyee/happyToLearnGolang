###						rune、string、bytes小解

​	在Go当中 string底层是用byte数组存的，并且是不可以改变的。

例如 `s:="Go编程" fmt.Println(len(s)) `输出结果应该是**8**因为中文字符是用3个字节存的。

​	`len(string(rune('编')))`的结果是3

​	如果想要获得我们想要的情况的话，需要先转换为rune切片再使用内置的len函数

​	`fmt.Println(len([]rune(s)))`结果就是4了。

​	所以用string存储unicode的话，如果有中文，按下标是访问不到的，因为你只能得到一个byte。 要想访问中文的话，还是要用rune切片，这样就能按下表访问。

​	参考资料：

​	１、[rune、string、bytes的区别](http://www.golangtc.com/t/528cc004320b52227200000f)

