### 软硬件环境

* Windows 10
* Go 1.8

### 什么是接口？

接口定义了一组方法，但是这些方法不包含实现的代码，另外接口里也不能包含变量。如下定义了一个接口

```
type intfNamer interface {
    Method1(param_list) return_type
    Method2(param_list) return_type
    ...
}
```

### 为什么要设计接口？

Go不是传统意义上的面向对象编程语言，它没有类及其继承的概念。接口是一种契约，实现类型必须满足它，它描述了类型的行为，规定类型可以做什么。接口彻底将类型能做什么，以及如何做分离开来，使得相同接口的变量在不同的时刻表现出不同的行为，这就是多态的本质。

### 简单接口示例

```
package main

import (
    "fmt"
)

type namer interface {
    area() int
}

type rect struct {
    width,height int
}

type square struct {
    side int
}

func (r rect) area() int {
    return r.height * r.width
}

func (s square) area() int {
    return s.side * s.side
}

func main() {
    var a = rect{4,3}
    var b  = square{6}

    fmt.Println(a.area())
    fmt.Println(b.area())
}
```

程序执行结果为

![mtqq_mosquitto](https://raw.githubusercontent.com/djstava/PostsCollection/master/images/go/go_intf_01.png)

### 测试某个值是否实现了某个接口?

```
type Stringer interface {
    String() string
}

if sv, ok := v.(Stringer); ok {
    fmt.Printf("v implements String(): %s\n", sv.String()) // note: sv, not v
}
```

### 接口嵌套

一个接口可以包含一个或者多个其它的接口，如下

```
type ReadWrite interface {
    Read(b Buffer) bool
    Write(b Buffer) bool
}

type Lock interface {
    Lock()
    Unlock()
}

type File interface {
    ReadWrite
    Lock
    Close()
}
```

File接口包含了ReadWrite接口、Lock接口和方法Close，任何实现了File接口的类型同时也实现了接口ReadWrite和Lock

### 需要注意的点

* 类型不需要显式地声明它实现了某个接口，接口被隐式地实现，多个类型可以实现同一个接口
* 一个类型可以实现多个接口
* 实现了某个接口的类型，可以有其它的方法

### 接口方法中类型T和T*的区别

```
type typeA struct {

}

type intfA interface {
    func1()
}

func (a *typeA) func1() {

}

func func2(i intfA)  {
    i.func1()
}

var a typeA
```

上面的例子中，在a上调用func2时，会导致一个编译器错误，因为func2需要一个intfA，而它的方法func1之定义在指针上。

Go 语言规范定义了接口方法集的调用规则：

* 类型 *T 的可调用方法集包含接受者为 *T 或 T 的所有方法集
* 类型 T 的可调用方法集包含接受者为 T 的所有方法
* 类型 T 的可调用方法集不包含接受者为 *T 的方法

### 小结

综上所述，接口就是一组方法的集合。 只要一个类型实现了某个接口中的方法，那么，这个接口类型的变量就可以接受这个类型的变量，而且同一个接口，可以接收不同类型的变量。

