---
title: Go-Method+Interface
date: 2024-10-09 11:04:30
tags: Go 
---
# 方法和接口

---

## 方法：Method--以及选取接收者的学问

### 1.方法 介绍

- 方法就是一类指定**接收者**这一参数的函数
- 你可以理解为，方法是一种对某一类变量均能使用的**性质函数**
- 方法的声明&定义方式：

  ```go
  //方法接收者在参数列表内，位于func关键字和方法名之间
  func (receivers) <method name>(parameter table) (returned value table){
      function body
  }
  ```

  例如下面这个例子，其中的Abs()就是针对Vertex类型结构体变量的方法。

  ```go
  package main

  import (
  	"fmt"
  	"math"
  )

  type Vertex struct {
  	X, Y float64
  }

  func (v Vertex) Abs() float64 {
  	return math.Sqrt(v.X*v.X + v.Y*v.Y)
  }
  ```
- 方法的调用：

  对于结构体变量，方法的调用类似于结构体变量名的使用，用点号来进行调用。

  ```go
  v.Method()  //v is of type Vertex
  //其中，Method()方法也可以是针对结构体指针的方法
  ```

  ```go
  func main() {
  	v := Vertex{3, 4}
  	fmt.Println(v.Abs())//在方法调用时也能体现方法和函数的一些区别
  }
  ```

### 2.方法使用的补充

你也可以为非结构体类型声明方法。

在此例中，我们看到了一个带 `Abs` 方法的数值类型 `MyFloat`。

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

你只能为在**同一包内定义**的类型的接收者声明方法，而不能为其它包内定义的类型（包括 `int` 之类的内建类型）的接收者声明方法。

（译注：就是接收者的类型定义和方法声明必须在同一包内；**不能为内建类型声明方法**。）

### 3.指针接收者

> 接收者参数为**结构体变量指针**的方法函数

- 为什么要用指针作为接收者参数？

  答：因为对于一个结构体，经常会用到需要修改元素值的方法。即需要修改原**接收者本身的值**。这种情况下我们必须使用指针接收者的方法。

  *结合指针的相关知识我们知道，在调用函数时，不以指针为参进行的修改**只能修改形参**，无法做到**实质性改变**。*

  ```go
  type Vertex struct {
  	X, Y float64
  }

  func (v Vertex) Abs() float64 {
  	return math.Sqrt(v.X*v.X + v.Y*v.Y)
  }

  func (v *Vertex) Scale(f float64) {
  	v.X = v.X * f
  	v.Y = v.Y * f
  }
  //指针接收者：最终结果为：50。即Scale方法是有效的
  //假如将参数中的‘*’去掉，那么最终结果为5.即Scale方法实际上无效。

  func main() {
  	v := Vertex{3, 4}
  	v.Scale(10)//在此处，尽管v不是指针类型变量，但也可以直接调用属于该结构体指针变量的方法
  	fmt.Println(v.Abs())
  }
  ```
- 方法与指针的重定向

  而以指针为接收者的方法被调用时，接收者**既能为值又能为指针**：

  ```
  var v Vertex
  v.Scale(5)  // OK
  p := &v
  p.Scale(10) // OK
  ```

  对于语句 `v.Scale(5)`，即便 `v` 是个值而非指针，带指针接收者的方法也能被直接调用。 也就是说，由于 `Scale` 方法有一个指针接收者，为方便起见，**Go 会将语句 `v.Scale(5)` 解释为 `(&v).Scale(5)`**。

## 接口：Interface--试图赋予一类相似内容一个统一的属性方法

> 这一块内容的描述在 [菜鸟教程-Go语言接口]([Go 语言接口 | 菜鸟教程 (runoob.com)](https://www.runoob.com/go/go-interfaces.html)) 里讲的比较清晰。主要是接口这个概念比较抽象，有时间的话也需要去了解一下“类”的相关知识。

### 1.接口介绍

- **接口类型**是由一组方法签名定义的集合。

  常见的接口类型需要以下步骤：

  1. 定义接口
  2. 定义结构体类型（特定的一些变量类型）
  3. 说明这些类型实现了该接口
  4. 针对不同类型，定义接口中所实现的方法的具体内容
  5. 将接口作为参数，实现对不同类型的统一调用

### 2.Go中的接口

> - Go 语言提供了另外一种数据类型即接口，它把所有的**具有共性的方法**定义在一起，任何其他类型只要实现了这些方法就是实现了这个接口。--*隐式实现*
> - 一个接口代表了**一类**同名且格式限定的方法（可以是限定返回值，也可以是限定参数，也可以两者都限定）

- **接口的定义：**

```go
/* 定义接口 */
type interface_name interface {
   method_name1 [return_type]
   method_name2 [return_type]
   method_name3 [return_type]
   ...
   method_namen [return_type]
}


/*一些示例*/
type Abser interface {
	Abs() float64//代表一类名字为Abs() 返回值类型为float64的方法集合
	//可以理解为，各种各样的结构体类型都可以拥有一个返回值为float64的Abs()方法
}

type Writer interface {
    Write(p []byte) (n int, err error)
}
```

- 接口可以让我们将不同的类型绑定到一组公共的方法上，从而实现多态和灵活的设计。
- **接口方法的实现**：

  ```go
  /* 定义结构体 */
  type struct_name struct {
     /* variables */
  }


  /* 实现接口方法 */
  func (struct_name_variable struct_name) method_name1() [return_type] {
     /* 方法实现 */
  }
  ...
  func (struct_name_variable struct_name) method_namen() [return_type] {
     /* 方法实现*/
  }
  ```
- Go 语言中的接口是**隐式实现**的，也就是说，如果一个类型实现了一个接口定义的所有方法，那么它就自动地实现了该接口。
- 在编写对应方法的过程中实现了接口。
- **接口的值与接口的运用：**

  1. 接口类型变量到底存储的是什么？

     接口类型变量可以存储任何实现了该接口的**类型的值**。
  2. 通过将接口作为参数来实现对不同类型的调用，从而实现多态。
- 下面的例子都很好地展现了接口的使用和功能

  ```go
  /*计算不同类型图形的面积*/

  package main

  import "fmt"


  //Shape接口，定义了一个方法area()，“包含”了后面各种类型的图形，而这一步是通过隐式实现来进行“联系”的
  type Shape interface {
      area() float64
  }



  type Rectangle struct {
      width  float64
      height float64
  }

  func (r Rectangle) area() float64 {
      return r.width * r.height
  }
  //Rectangle类型通过实现了area()方法，实现了Shape接口


  type Circle struct {
      radius float64
  }

  func (c Circle) area() float64 {
      return 3.14 * c.radius * c.radius
  }
  //Circle类型通过实现了area()方法，实现了Shape接口



  func main() {
      var s Shape
      //定义了一个Shape类型的变量
      //也就是定义了一个变量，它的类型，是实现了Shape接口的所有变量类型的并集构成的类型

      s = Rectangle{width: 10, height: 5}
      fmt.Printf("矩形面积: %f\n", s.area())

      s = Circle{radius: 3}
      fmt.Printf("圆形面积: %f\n", s.area())
      //所以，它可以被赋予任何实现该接口的类型的值
      //从而在调用接口方法时，看上去就是统一的。

  }
  ```

  这个例子里，针对统一的一个接口方法，对不同类型进行了**有差异的定义**（比如Circle类型的area()和Rectangle()类型的area()的计算方法肯定不同）

  而对于一些**共性的处理**时，我们可以直接将接口类型作为参数去编写一些函数。

  ```go
  package main

  import (
  	"fmt"
  	"math"
  )

  type I interface {
  	M()
  }

  type T struct {
  	S string
  }

  func (t *T) M() {
  	fmt.Println(t.S)
  }

  type F float64

  func (f F) M() {
  	fmt.Println(f)
  }

  func main() {
  	var i I

  	i = &T{"Hello"}
  	describe(i)
  	i.M()

  	i = F(math.Pi)
  	describe(i)
  	i.M()
  }

  //直接将I接口类型作为参数
  //即该函数能够适用于所有实现I接口的变量
  func describe(i I) {
  	fmt.Printf("(%v, %T)\n", i, i)
  }

  ```

### 3.nil、空接口

- 接口中的nil值和“空”：

  - 底层值为 `nil`：即某一属于实现接口的类型的变量值为 `nil`，但是其他接口要素都齐全。
  - `nil`接口值：即接口只有定义的躯干部分（有抽象方法），**没有对应的具体方法**，没有任何**实现该接口的类型**。所以 `nil`接口值既不保存值也不保存具体类型。
  - 空接口：指定了零个方法的接口值。

    ```go
    interface{}
    ```

    空接口可保存任何类型的值。（因为每个类型都至少实现了0个方法，相当于所有类型都实现了这个接口）。

    空接口被用来处理未知类型的值。例如，`fmt.Print` 可接受类型为 `interface{}` 的任意数量的参数。

    ```go
    package main

    import "fmt"

    func main() {
    	var i interface{}
    	describe(i)
        //输出结果为：(<nil>, <nil>)
        //在此处无论是值还是类型都是nil


    	i = 42//通过直接存数据的方式，间接地确认了i的底层类型
    	describe(i)
        //输出结果为：（42,int）


    	i = "hello"
    	describe(i)
        //输出结果为：(hello,string)


    }

    func describe(i interface{}) {
    	fmt.Printf("(%v, %T)\n", i, i)
    }


    ```
  - 你甚至可以定义一个接收任意类型的function/array/slice/map/struct

    ```go
    package main

    import "fmt"

    func main() {
        any := make([]interface{}, 5)
        any[0] = 11
        any[1] = "hello world"
        any[2] = []int{11, 22, 33, 44}
        //any切片的不同单元存储了不同类型的数据，这就是通过空接口来实现的

        for _, value := range any {
            fmt.Println(value)
        }
        //对 any数组的range遍历
    }
    ```
  - 但是注意，不能把空接口类型的对象赋值给某一个固定类型。尽管你的空接口对象先前已经被该种类型的值赋值了。如下

    ```go
    package main

    func main() {
        // 声明a变量, 类型int, 初始值为1
        var a int = 1

        // 声明i变量, 类型为interface{}, 初始值为a, 此时i的值变为1
        var i interface{} = a

        // 声明b变量, 尝试赋值i
        var b int = i
        //这一步会报错
        //11:14: cannot use i (variable of type interface{}) as int value in variable declaration: need type assertion

    }
    ```

### 4.类型断言&选择

- 类型断言：

  **类型断言** 提供了访问接口值底层具体值的方式。

  - 方法一：

    ```go
    t := i.(T)
    ```

    该语句断言接口值 `i` 保存了具体类型 `T`，**并将其底层类型为 `T` 的值赋予变量 `t`**。若 `i` 并未保存 `T` 类型的值，该语句就会触发一个恐慌。
  - 方法二：

    为了 **判断** 一个接口值是否保存了一个特定的类型，**类型断言可返回两个值**：其**底层值**以及一个**报告断言是否成功的布尔值**。

    ```go
    t, ok := i.(T)
    ```

    若 `i` 保存了一个 `T`，那么 `t` 将会是其底层值，而 `ok` 为 `true`。

    否则，`ok` 将为 `false` 而 `t` 将为 `T` 类型的零值，程序并不会产生恐慌。

    > 这个方法和映射的读取非常类似
    >
- 类型选择：

  **类型选择** 是一种按顺序从几个类型断言中选择分支的结构。

  类型选择与一般的 switch 语句相似，不过类型选择中的 case 为类型（而非值）， 它们针对给定接口值所存储的值的类型进行比较。

  ```go
  switch v := i.(type) {//v :=i.(type)正是类型断言的语法
  case T:
      // v 的类型为 T
  case S:
      // v 的类型为 S
  default:
      // 没有匹配，v 与 i 的类型相同
  }
  ```

  类型选择中的声明与类型断言 `i.(T)` 的语法相同，只是具体类型 `T` 被替换成了关键字 `type`。

  - 一个例子

    ```go
    /*实现do()函数，能够针对i的不同类型的做出不同操作*/
    package main

    import "fmt"

    func do(i interface{}) {
    	switch v := i.(type) {
    	case int:
    		fmt.Printf("Twice %v is %v\n", v, v*2)
    	case string:
    		fmt.Printf("%q is %v bytes long\n", v, len(v))
    	default:
    		fmt.Printf("I don't know about type %T!\n", v)
    	}
    }

    func main() {
    	do(21)
    	do("hello")
    	do(true)
    }
    //输出分别为：
    //Twice 21 is 42
    //"hello" is 5 bytes long
    //I don't know about type bool!
    ```

### 5.常用内建接口

#### `Stringer`-可修改某变量默认的输出格式

```go
type Stringer interface {
    String() string
}
```

`Stringer` 是一个可以用字符串描述自己的类型。`fmt` 包（还有很多包）都通过此接口来打印值。**并且！!接口方法的引用可以直接通过fmt包中各种print函数直接打印变量实现**

```go
package main

import "fmt"

type Person struct {
	Name string
	Age  int
}

//通过String()方法的定义，隐式说明了Person结构类型实现了Stringer接口
func (p Person) String() string {
	return fmt.Sprintf("%v (%v years)", p.Name, p.Age)
}
//该函数返回的是fmt.Sprintf()函数打印出的字符串

func main() {
	a := Person{"Arthur Dent", 42}
	z := Person{"Zaphod Beeblebrox", 9001}
	fmt.Println(a.String())
	fmt.Println(z.String())
    fmt.Println(a, z)
}
//输出结果为：
//Arthur Dent (42 years)
//Zaphod Beeblebrox (9001 years)
//Arthur Dent (42 years) Zaphod Beeblebrox (9001 years)

//前两个引用和后一个直接输出效果是一样的。


```

#### `Error`

- Go 程序使用 `error` 值来表示错误状态。与 `fmt.Stringer` 类似，`error` 类型是一个内建接口：

```go
type error interface {
    Error() string
}
```

（与 `fmt.Stringer` 类似，`fmt` 包在打印值时也会满足 `error`。）

- 通常函数会返回一个 `error` 值，调用的它的代码应当判断这个错误是否等于 `nil` 来进行错误处理。

```go
i, err := strconv.Atoi("42")//函数的两个返回值，此处的err相当于短变量声明
if err != nil {//假如非nil，则该函数调用失败
    fmt.Printf("couldn't convert number: %v\n", err)
    return
}
fmt.Println("Converted integer:", i)
```

**`error` 为 nil 时表示成功；非 nil 的 `error` 表示失败。**

- 另外，与 `Stringer`的用法有些类似。你可以通过对函数返回error类型值的类型限定，再通过实现特定类型的 `Error()`接口方法，自定义报错信息。（比如下面这个例子）

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

  func (e *MyError) Error() string {//针对本程序建立的error返回值类型实现的Error()方法
  	return fmt.Sprintf("at %v, %s",
  		e.When, e.What)
  }

  func run() error {//与常规函数不同，仅用于本例，这是一个报错/只返回error值的函数
  	return &MyError{
  		time.Now(),
  		"it didn't work",
  	}
  }

  func main() {
  	if err := run(); err != nil {//err非nil
  		fmt.Println(err)
  	}
  }


  ```

[Go error 处理的四种方式 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/441420411)

#### `Reader`

- `io` 包指定了 `io.Reader` 接口，它表示从数据流的末尾进行读取。

  Go 标准库包含了该接口的[许多实现](https://go-zh.org/search?q=Read#Global)，包括文件、网络连接、压缩和加密等等。

  `io.Reader` 接口有一个 `Read` 方法：

  ```go
  func (T) Read(b []byte) (n int, err error)//对任意类型T
  ```

  **`Read` 用数据填充给定的字节切片并返回填充的字节数和错误值**。在遇到数据流的结尾时，它会返回一个 `io.EOF` 错误。
- 示例代码创建了一个 [`strings.Reader`](https://go-zh.org/pkg/strings/#Reader) 并以每次 8 字节的速度读取它的输出。

```go
package main

import (
	"fmt"
	"io"
	"strings"
)

func main() {
	r := strings.NewReader("Hello, Reader!")//string包中的一个函数

	b := make([]byte, 8)//构建一个接收切片
	for {
		n, err := r.Read(b)
		fmt.Printf("n = %v err = %v b = %v\n", n, err, b)
		fmt.Printf("b[:n] = %q\n", b[:n])
		if err == io.EOF {//到了数据流末尾，返回一个io.EOF错误
			break
		}
	}
}
//返回结果为：
//n = 8 err = <nil> b = [72 101 108 108 111 44 32 82]
//b[:n] = "Hello, R"
//n = 6 err = <nil> b = [101 97 100 101 114 33 32 82]
//b[:n] = "eader!"
//n = 0 err = EOF b = [101 97 100 101 114 33 32 82]
//b[:n] = ""


```

#### `image`

- [`image`](https://go-zh.org/pkg/image/#Image) 包定义了 `Image` 接口：

  ```
  package image

  type Image interface {
      ColorModel() color.Model
      Bounds() Rectangle
      At(x, y int) color.Color
  }
  ```
  **注意:** `Bounds` 方法的返回值 `Rectangle` 实际上是一个 [`image.Rectangle`](https://go-zh.org/pkg/image/#Rectangle)，它在 `image` 包中声明。
- （请参阅[文档](https://go-zh.org/pkg/image/#Image)了解全部信息。

  `color.Color` 和 `color.Model` 类型也是接口，但是通常因为直接使用预定义的实现 `image.RGBA` 和 `image.RGBAModel` 而被忽视了。这些接口和类型由 [`image/color`](https://go-zh.org/pkg/image/color/) 包定义。
