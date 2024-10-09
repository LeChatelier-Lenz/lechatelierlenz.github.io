---
title: Go-basic
date: 2024-10-09 11:02:24
tags:
---


> 本"Go-"系列记录了Golang学习的一些笔记摘要，内容主要影响自[Go 语言之旅 (go-zh.org)](https://tour.go-zh.org/list)


# 基础部分

---

## 基础要素

> package variable function

### 1.package：包和导入导出

> *每个Go程序都是由包构成的*
>
> 可以把Go当中的包某种程度上看作C中的库

在Go中，每一个完整的程序都是一个package，它可以是语言本身自带的语法库，也可以是使用者自己编写的某段代码

- 包的导入 `import`

  统一用 `import`来进行程序包的导入，通常用如下方式进行引用

  ```go
  import "fmt"

  import (	//分组导入
  	"fmt"
      "math/rand"
  )
  ```

  需要注意的是，在包的引入语法当中，默认import导入语法中最终用到的包名与路径中的最后一个元素一致。比如对于路径 "math/rand"， 最终导入的包是package rand
- 包的导出名-Go自带的函数/已定义常量

  在Go中，如果一个名字用**大写字母**开头，那么它就是已导出的。同时这些导出名往往有对应的导出包。

  ```go
  package main

  import (
  	"fmt"
  	"math"
  )

  func main() {
  	fmt.Println(math.Pi)//math包中的导出名Pi，代表圆周率常量
  }
  ```

  运行结果为“3.141592653589793”

### 2.function：Go中的函数

#### 函数初步

- 函数定义方法

  ```go
  func <FunctionName> (parameter table:parameter1 type,parameter2 type,...) (returned value table:returned value1 type,returned value2 type,...) {
      //function definition content
      return returned value
  }

  ```

  其中当连续两个或多个形参（返回值）的类型相同时，除最后一个类型以外，其他都可以省略

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
- 函数可以没有参数或接受多个参数
- Go中函数可以实现多值返回

#### 函数进阶

- 函数作为函数的参数或返回值

  ```go
  package main

  import (
  	"fmt"
  	"math"
  )

  /*将函数作为参数*/
  func compute(fn func(float64, float64) float64) float64 {
  	return fn(3, 4)
  }

  func main() {
      /*函数的短变量声明*/
  	hypot := func(x, y float64) float64 {
  		return math.Sqrt(x*x + y*y)
  	}

  	fmt.Println(hypot(5, 12))
      //结果为 13
  	fmt.Println(compute(hypot))
      //结果为 5
  	fmt.Println(compute(math.Pow))
      //结果为 81
  }
  ```
- 函数的闭包：

> Go 函数可以是一个闭包。闭包是一个函数值，它引用了其函数体之外的变量。该函数可以访问并赋予其引用的变量的值，换句话说，该函数被这些变量“绑定”在一起。

还没有完全领略它的强大，暂时先把一些案例代码展示出来。

```go
package main

import "fmt"

func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func main() {
	pos, neg := adder(), adder()
	for i := 0; i < 10; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}
}
//结果是i和-2*i的累加和
```

斐波那契闭包：

```go
package main

import "fmt"

// fibonacci is a function that returns
// a function that returns an int.
func fibonacci() func() int {
	a := -1
	b := 1
	return func() int {
	    r := a + b
	    a, b = b, a + b
		return r
	
	}
}

func main() {
	f := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(f())
	}
}

```

### 3.variable：变量和类型

- 变量声明&初始化

  变量声明：`var <variablename> type`  关键字：`var`

  ```go
  package main

  import "fmt"

  var c, python, java bool//声明多个相同类型变量

  func main() {
  	var i int
  	fmt.Println(i, c, python, java)
  }
  //输出结果：0 false false false
  ```

  变量初始化：`var <variablename> = value`

  ```go
  var i, j int = 1, 2

  func main() {
  	var c, python, java = true, false, "no!"
  	fmt.Println(i, j, c, python, java)
  }

  //注意灵活运用Go中独特的对应多变量同时赋值方式
  ```

  短变量声明：`<newVariableName> := value`

  > 简介赋值语句 `:=`可在类型明确的地方代替var声明
  >
  > 声明+初始化
  >

  ```go
  var i, j int = 1, 2
  k := 3
  c, python, java := true, false, "no!"
  ```

  注意！使用 `var`关键字的变量声明在函数内外均可以进行，但是使用 `:=`进行短变量声明的语句只能在函数体内使用。否则会报错。

> ```shell
> ./prog.go:5:1:syntax error: non-declaration statement outside function body
> ```

- 基本类型-默认未初始化值为零值

  **bool** 零值为 `false`

  **string**  零值为 `""`(空字符串)

  **int int8 int16 int32 int64**

  **uint uint8 uint16 uint32 uint64** 【int uint 一般根据系统位数不同默认为不同位宽】

  **byte** //uint8的别名

  **rune** //int32的别名 表示一个Unicode码点

  **float32 float64**

  **complex64 complex128**

  数值类型零值均为 `0`
- 类型转换

  使用方式：`var u = T(v)`或者 `u:= T(v)`

  其中T()代表将某数值/变量转化为T类型的内容

  ```go
  func main() {
  	var x, y int = 3, 4
  	var f float64 = math.Sqrt(float64(x*x + y*y))
  	var z uint = uint(f)
  	fmt.Println(x, y, z)
  }
  //运行结果为3，4，5

  func main() {
  	var x, y int = 3, 4
  	var f float64 = math.Sqrt(x*x + y*y)
  	var z uint = uint(f)
  	fmt.Println(x, y, z)
  }
  //报错，cannot use x * x + y * y (value of type int) as float64 value in argument to math.Sqrt
  //此处math.Sqrt()是一个针对float64类型的导出名函数，只能输入&输出float64类型的数据。也就是说，在这些情况下，赋值时需要显式转换。
  ```
- 类型推导（与C相似）

  在声明一个变量**而不指定其类型时**（即使用不带类型的 `:=` 语法或 `var =` 表达式语法），变量的类型*由右值推导*得出。

  “推导”：

  1. 若右值类型确定，则左边新变量类型与其相同
  2. 若右值为数值常量，则根据该数值常量的精度自动确认左边新变量的类型

  ```go
  var i int
  j := i // j 也是一个 int

  i := 42           // int
  f := 3.142        // float64
  g := 0.867 + 0.5i // complex128
  ```

  > 在左边变量类型已经确定的赋值语句中，右值的精度永远只能小于等于左边变量精度
  >
  > ```go
  > func main() {
  > 	var v int
  > 	v = 3.23 // 修改这里！
  > 	fmt.Printf("v is of type %T\n", v)
  > 	fmt.Println(v)
  > }
  > //./prog.go:7:6: cannot use 3.23 (untyped float constant) as int value in assignment (truncated)
  > ```
  >
- 常量声明：`const <constantName> = value` 常量必须在声明时赋值

## 流程控制

> for(循环)
>
> if-else/switch(分支)
>
> defer(推迟)

### 1.for：唯一的循环方式

- 使用方法：

  ```go
  for <初始化语句> ; <条件表达式> ; <后置语句> {
      迭代部分
  } 
  //与C不同，for循环中的大括号{}不可省略


  /*常规用法*/
  func main() {
  	sum := 0
  	for i := 0; i < 10; i++ {
  		sum += i
  	}
  	fmt.Println(sum)
  }


  /*while循环用法*/
  /*初始化和后置为空时，变为条件循环，即此时的for等价于C中的while*/
  func main() {
  	sum := 1
  	for ; sum < 1000; {
  		sum += sum
  	}
  	fmt.Println(sum)
  }
  //或者也可以把分号去掉
  func main() {
  	sum := 1
  	for sum < 1000 {
  		sum += sum
  	}
  	fmt.Println(sum)
  }

  /*无限循环*/
  /*相当于初始化、后置语句，条件表达式全部为空*/
  func main() {
  	for {
  	}
  }
  ```

### 2.if-else/switch：选择分支

- `if-else`

  - 使用方法： `if 条件表达式 {分支作用域}`
  - `if `无需小括号，**大括号必须有**
  - 在 `if` 作用域内声明的变量作用域仅在 `if`和对应的 `else`之内 "局部变量的生命周期"

  ```go
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
- `switch`

  - `switch`始终按照**自上而下**的顺序进行求值
  - 与C不同，Go中的 `switch`自动在每个 `case`后面添加了 `break`语句，每一个分支会自动停止。
  - **没有条件的switch**：相当于 `switch true`，从上至下第一个满足 `case`为 `true`的条件则会被执行

### 3.defer：推迟执行

- defer 语句会将函数推迟到**外层函数返回**之后执行。

  推迟调用的函数其参数会立即求值，但直到外层函数返回前该函数都不会被调用
- `defer` 的***压栈逻辑***

  推迟的函数调用会被压入一个栈中。当外层函数返回时，被推迟的函数会按照**后进先出**的顺序调用。

  > 可以理解成从上往下将函数压倒一个桶里，只有最上面的（往往就是最晚塞进去的）先取出来，才能去取下面的（往往就是最早塞进去的）。
  >

  ```go
  package main

  import "fmt"

  func main() {
  	fmt.Println("counting")

  	for i := 0; i < 10; i++ {
  		defer fmt.Println(i)
  	}//从0到9压入栈中

  	fmt.Println("done")
  }
  //运行结果是：
  /*
  counting
  done
  9
  8
  7
  6
  5
  4
  3
  2
  1
  0
  */
  /*从9到0取出*/
  ```

## Go的进阶数据类型

> pointer struct array slice(切片) map(映射)

### 1.指针

- 声明方式：

  ```go
  var p *T 
  //标名为T类型的指针，保存的是值的内存地址
  ```

  用 `&`操作符会生成一个指向该操作数的指针
  用 `*`可以间接引用指针指向的底层值（也叫做重定向）

  上述概念与C语言中的指针一致。但是在Go中，没有指针运算，与C不一致

### 2.结构体

- 声明和赋值：

  ```go
  /*声明方式*/
  type <struct name> struct {
      element1 type
      element2 type
      ...
  }

  /*赋值方法*/
  //通常可以用 <struct name>{element1 value, element2 value,...}来代表一个结构体常量
  v := Vertex{1, 2}
  fmt.Println(Vertex{1, 2})

  ```
- 结构体字段使用：

  ```go
  /*直接使用结构体变量名+点号进行使用*/
  v := Vertex{1, 2}
  v.X = 4

  /*结构体指针名+点号也同样可以*/
  v := Vertex{1, 2}
  p := &v
  p.X = 1e9
  ```
- **结构体文法：**

  1. 非 `Name : Value`语法，按序对应赋值
  2. 非 `Name : Value`语法，根据列出字段赋值，其他字段默认为零值。且全程与字段名的顺序无关

  ```go
  type Vertex struct {
  	X, Y int
  }

  var (
      v1 = Vertex{Y：1,X: 2}  // 创建一个 Vertex 类型的结构体,
  	v2 = Vertex{X: 1}  // Y:0 被隐式地赋予
  	v3 = Vertex{}      // X:0 Y:0
  	p  = &Vertex{1, 2} // 创建一个 *Vertex 类型的结构体（指针）
  )

  func main() {
  	fmt.Println(v1, p, v2, v3)
  }

  //运行结果为：
  {2 1} &{1 2} {1 0} {0 0}
  ```

### 3.数组【大小固定】

- 类型 `[n]T`表示拥有 `n`个类型为 `T`的值的数组

  > 与C语言一样，Go中的数组下标为0~n-1
  >
- 定义/初始化/短变量声明方法

```go
/*定义*/
var a [4]string //注意变量名和[]之间有一个空格！

/*初始化*/
//可以使用数组元素一一赋值
a[0] = "hello"
a[1] = "world"
a[2] = "fuck"
a[4] = "you"

//也可以使用分组赋值
a = [4]string{"hello","world","fuck","you"}

/*短变量声明*/
primes := [6]int{2,3,5,7,9,11}

```

### 4.切片【动态大小】

- **切片文法**：类型 `[]T` 表示一个元素类型为 `T` 的切片

  ```go
  //切片文法类似于没有长度的数组文法

  /*定义*/
  var s []int//与数组声明的区别就是，此时[]中不需要填数组

  /*短变量声明*/
  r := []bool{true, true, false}
  //会创建一个[3]bool{true, true, false}的数组，同时构建了一个引用它的切片

  s := []struct {
  	i int
  	b bool
  }{
  	{2, true},
  	{3, false},
  	{5, true},
  	{7, true},
  	{11, false},
  	{13, true},
  }
  //一个结构体数组切片的短变量声明
  ```
- **切片的值**：

  切片通过两个下标来界定，即一个上界和一个下界，二者以冒号分隔：

  ```go
  a[low : high]
  ```

  它会选择一个**半开区间**，包括第一个元素，但排除最后一个元素。

  以下表达式创建了一个切片，它包含 `a` 中下标从 1 到 3 的元素：

  ```go
  a[1:4]
  ```
- **切片下标**：

  注意！切片变量的下标不一定与原数组下表一致！

  切片的下标范围为0~L-1（其中L为切片元素长度）

  也就是说切片的初始下标对应引用原数组时的左边界下标值，但不一定相等。

下面是一段简单的切片使用代码段

```go
package main

import "fmt"

func main() {
	primes := [6]int{2, 3, 5, 7, 11, 13}

	var s []int = primes[1:4]
	fmt.Println(s)
}
```

- **切片对数组的运用**

  切片并**不存储任何数据**，它只是描述了底层数组中的一段。

  更改切片的元素*会修改*其底层数组中对应的元素。

  与它**共享底层数组的切片**都会观测到这些修改。

  ```go
  package main

  import "fmt"

  func main() {
  	names := [4]string{
  		"John",
  		"Paul",
  		"George",
  		"Ringo",
  	}
  	fmt.Println(names)

  	a := names[0:2]
  	b := names[1:3]
  	fmt.Println(a, b)

  	b[0] = "XXX"
  	fmt.Println(a, b)
  	fmt.Println(names)
  }
  //运行结果为：
  [John Paul George Ringo]
  [John Paul] [Paul George]
  [John XXX] [XXX George]
  [John XXX George Ringo]
  ```
- **切片的默认行为**

  切片下界的默认值为 `0`，上界则是该切片的长度。

  对于数组

  ```go
  var a [10]int
  ```

  来说，以下切片是等价的：

  ```go
  a[0:10]
  a[:10]
  a[0:]
  a[:]
  ```
- **切片的长度和容量**

  切片长度：切片所包含的元素个数。可用 `len(s)`获取

  切片容量：从切片的第一个元素开始数，到其**底层数组**元素末尾的个数。可用 `cap(s)`获取

  切片的**零值**是 `nil`。nil切片的长度和容量为0且没有底层数组

  【切片容量代表着切片可以拓展的数据，切片长度代表着下一次基于此切片的切片的默认上界】

```go
package main

import "fmt"

func main() {
	s := []int{2, 3, 5, 7, 11, 13}
	c := []int{2, 3, 5, 7, 11, 13}
	printSlice(s)

	// 截取切片使其长度	为 0, 不影响容量
	s = s[:0]
	printSlice(s)

	// 拓展其长度， 不影响容量
	s = s[:4]
	printSlice(s)

	// 舍弃前两个值 影响容量，且相当于前两个元素无法再获取
	s = s[2:]
	printSlice(s)

    //又一次抛弃了前一个值（其实是原数组的第三个值），切片容量进一步减少
	s = s[1:4]
	printSlice(s)

	s = s[2:]
	printSlice(s)

	c = c[2:]
	printSlice(c)
}

func printSlice(s []int) {
	fmt.Printf("len=%d cap=%d %v\n", len(s), cap(s), s)
}
```

**切片的实质是一个数组片段的描述**。它包含了**指向数组的指针**，**片段的长度**和**容量**（片段的最大长度）

### 5.基于切片的操作

#### (1)**用 `make`创建动态数组/切片**

```go
a := make([]T,len, cap)
//make函数会分配一个元素为零值，长度为cap的数组。并返回一个引用了它的长度为len的切片。
//当只有两个参数时，默认len和cap一样。
```

`make`用第二个参数限定切片长度，用第三个参数限定底层数组长度。并全赋零值。

```go
package main

import "fmt"

func main() {
	a := make([]int, 5)
	printSlice("a", a)
    //输出为：a len=5 cap=5 [0 0 0 0 0]
  

	b := make([]int, 0, 5)
	printSlice("b", b)
    //输出为：b len=0 cap=5 []

  
	c := b[:4]
	printSlice("c", c)
    //输出为：c len=2 cap=5 [0 0 0 0]

    //假如在此处的内容是：
    //c := b[1:]
    //将会报错，因为默认上界是该切片的长度（也就是b的长度，在这里是0）
  
    //但是如果是b[1:2] b[1:3],则都可以，因为此时是根据切片底层数组的长度为依据进行分配
  
	d := c[2:]
	printSlice("d", d)
    //输出为：d len=3 cap=3 [0 0]

    /*上述结果均与前文总结一致*/
}

func printSlice(s string, x []int) {
	fmt.Printf("%s len=%d cap=%d %v\n",
		s, len(x), cap(x), x)
}
```

#### (2)**用 `append`向切片追加元素**-切片的生长

```go
func append(s []T, vs ...T) []T
```

`append` 的第一个参数 `s` 是一个元素类型为 `T` 的切片，其余类型为 `T` 的值将会**追加**到该切片的末尾。

`append` 的结果是一个包含原切片所有元素加上新添加元素的切片。

```go
func main() {
	var s []int
	printSlice(s)
	//结果为：len=0 cap=0 []
  
	// 添加一个值为0的切片
	s = append(s, 0)
	printSlice(s)
    //结果为：len=1 cap=1 [0]

	// 这个切片会按需增长
	s = append(s, 1)
	printSlice(s)
    //结果为：len=2 cap=2 [0 1]

	// 可以一次性添加多个元素
	s = append(s, 2, 3, 4)
	printSlice(s)
    //结果为：len=5 cap=6 [0 1 2 3 4]
  
}

```

#### (3)**用 `range`来遍历切片**-结合 `for`循环遍历

```go
slice := []T{value1,value2,value3,...}

for i,v := range slice{
    fmt.Printf("%d %d",i,v)
}//i的类型始终是int,而v的类型由T决定

```

使用for和range循环遍历切片时，每次迭代都会返回两个值。第一个值为当前元素的**下标**，第二个值为该下标所对应元素的**一份副本**（就是把value传出去）

当然，我们也可以用 `_`来忽略下标或值的赋值

```go
for i, _ := range pow
for _, v := range pow
```

如果你只需要索引，忽略第二个变量即可

```go
for i := range pow
```

eg.输出2的n次方

```go
package main

import "fmt"

func main() {
	pow := make([]int, 10)
	for i := range pow {
		pow[i] = 1 << uint(i) // == 2**i
	}
	for _, value := range pow {
		fmt.Printf("%d\n", value)
	}
}
```

### 6.映射

> 概念理解：编程当中的“键”（key）和“映射”（map）
>
> 其实可以借用实体键盘来进行理解，即某一个特殊按键（key）对应着一个特别的值（value），进行绑定的过程就是所谓的映射（map）

- 映射搭建：三步走

  ```go
  /*第一步：起名*/
  var m map[string]float64
  //声明了一个变量，代表一种映射方法m，此时为映射的零值nil。既没有键，也不能添加键。


  /*第二步：初始化*/
  m = make(map[string]float64)
  //进行了初始化，此时会返回一个给定类型的初始映射，此时该映射本身不再为零值nil，但是它对于所有键的映射值均为零值。


  /*第三步：赋予映射方法实值*/
  m["Bell Labs"] = 40.68433
  //即键“Bell Labs”在映射m下的值为40.68433
  //实际上这是一种映射m的插入/修改方式

  ```
- 更常用的搭建方式

  实际上，如果在一开始就能够直接给出特定的键值对关系，那么就无需第二步的操作，同时还能将第三步和第一步结合。也就是通常所说的**一步到位**

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
	fmt.Println(m["Bell Labs"])
}
//输出结果为：
//{40.68433, -74.39967}
```

- **几个注意点**：

  1. 在这种文法方式下，其他键的值默认**均为零值**
  2. 注意在程序最后输出m和输出m[string1]的结果是**不一样的**。

     输出m，则会将整个映射方法展现出来

     而输出m[string1]，则只会输出m映射方法下string对应的value。
- 修改映射

  在映射 `m` 中插入或修改元素：

  ```go
  m[key] = elem
  ```

  获取元素：

  ```go
  elem = m[key]
  ```

  **删除元素**：

  ```go
  delete(m, key)
  ```

  通过**双赋值**检测某个键**是否存在**：

  ```go
  elem, ok = m[key]//映射的双返回
  ```

  ***若 `key` 在 `m` 中，`ok` 为 `true` ；否则，`ok` 为 `false`。***

  若 `key` 不在映射中，那么 `elem` 是该映射元素类型的零值。

  **注** ：若 `elem` 或 `ok` 还未声明，你可以使用短变量声明：

  ```go
  elem, ok := m[key]
  ```
