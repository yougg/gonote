## Google Go语言 golang 语法详解笔记

*Author*：cxy  
*Date*：2022-01-19  
*Version*：2.0  
*Source*：[Fork me on GitHub](https://github.com/yougg/gonote)  
*Description*：学习Go语言过程中记录下来的语法详解笔记，可以帮助新接触的朋友快速熟悉理解Golang，也可以作为查询手册翻阅。其中若有错误的地方还请指正，或者在GitHub直接fork修改。  
本文使用LiteIDE的Markdown编辑器编写，博客为LiteIDE导出的单页html，可以直接保存离线使用。

---

- [包 Package](#包-package)  
    - [包的声明 Declare](#包的声明-declare)  
    - [包的导入 Import](#包的导入-import)  
    - [包内元素的可见性 Accessability](#包内元素的可见性-accessability)  

- [数据类型 Data Type](#数据类型-data-type)  

    - [基础数据类型 Basic data type](#基础数据类型-basic-data-type)  
    - [变量 Variable](#变量-variable)  
    - [常量 Constant](#常量-constant)  
    - [数组 Array](#数组-array)  
    - [切片 Slice](#切片-slice)  
    - [字典/映射 Map](#字典映射-map)  
    - [结构体 Struct](#结构体-struct)  
    - [指针 Pointer](#指针-pointer)  
    - [通道 Channel](#通道-channel)  
    - [接口 Interface](#接口-interface)  
    - [自定义类型](#自定义类型)  

- [语句 Statement](#语句-statement)  

    - [分号/括号 ; {](#分号括号--)  
    - [条件语句 if](#条件语句-if)  
    - [分支选择 switch](#分支选择-switch)  
    - [循环语句 for](#循环语句-for)  
    - [通道选择 select](#通道选择-select)  
    - [延迟执行 defer](#延迟执行-defer)  
    - [跳转语句 goto](#跳转语句-goto)  
    - [阻塞语句 blocking](#阻塞语句-blocking)  

- [函数 Function](#函数-function)  

    - [函数声明 Declare](#函数声明-declare)  
    - [函数闭包 Closure](#函数闭包-closure)  
    - [内建函数 Builtin](#内建函数-builtin)  
    - [初始化函数 init](#初始化函数-init)  
    - [方法 Method](#方法-method)  

- [并发 Concurrency](#并发-concurrency)  

- [测试 Testing](#测试-testing)  

    - [单元测试 Unit](#单元测试-unit)   
    - [基准测试 Benchmark](#基准测试-benchmark)
    - [模糊测试 Fuzzing](#模糊测试-fuzzing)

- [泛型 Generics](#泛型-generics)

    - [类型约束](#类型约束-constraint)
    - [类型参数](#类型参数-parameters)

---

## <span id="包-package">**包 Package**</span>

### <span id="包的声明-declare">**包的声明 Declare**</span>

- 使用`package`关键字声明当前源文件所在的包  
  包声明语句是所有源文件的第一行非注释语句  
  包名称中不能包含空白字符  
  包名推荐与源文件所在的目录名称保持一致  
  每个目录中只能定义一个package

    ```go
    package abc     // 声明一个名为“abc”的包

    package 我的包   // 声明一个名为“我的包”的包

    package main    // main包, 程序启动执行的入口包
    ```

  错误的包声明
    ```go
    package "mypkg" // 错误

    package a/b/c   // 错误

    pakcage a.b.c   // 错误
    ```

### <span id="包的导入-import">**包的导入 Import**</span>

- 导入包路径是对应包在`$GOROOT/src/`、`$GOPATH/pkg/mod/`、`当前路径`或`go.mod`模块名声明中的相对路径

    ```go
    // 导入$GOROOT/src/中的相对路径包(官方标准库)
    import "fmt"
    import "math/rand"

    // 导入$GOPATH/pkg/mod/中的相对路径包
    import "github.com/user/project/pkg"
    import "code.google.com/p/project/pkg"
    ```

  导入当前包的相对路径包  
  例如有Go目录如下：  
  `MODULE`  
  　├─x0  
  　│　├─y0  
  　│　│　└─z0  
  　│　└─y1  
  　│　　└─z1  
  　└─x1  
  　　└─y2

    ```go
    import "./y0/z0"    // 在x0包中导入子包 z0包
    import "../y0/z0"   // 在y1包中导入子包 z0包
    import "x0/y1/z1"   // 在y2包中导入 z1包
    ```

  错误的导入包路径

    ```go
    import a/b/c        // 错误
    import "a.b.c"      // 错误
    import a.b.c        // 错误
    ```

- 用圆括号组合导入包路径

    ```go
    import ("fmt"; "math")

    import (
        "fmt"
        "math"
    )
    ```

- 导入包可以定义别名，防止同名称的包冲突

    ```go
    import (
        "a/b/c"

        c1 "x/y/c"     // 将导入的包c定义别名为 c1

        格式化 "fmt"    // 将导入的包fmt定义别名为 格式化

        m "math"       // 将导入的包math定义别名为 m
    )
    ```

- 引用包名是导入包路径的最后一个目录中定义的唯一包的名称  
  定义的包名与目录同名时，直接引用即可

    ```go
    // 引用普通名称的导入包
    c.hello()

    // 引用定义别名的包
    格式化.Println(m.Pi)
    ```

  定义的包名与所在目录名称不同时，导入包路径仍为目录所在路径，引用包名为定义的包名称

    ```go
    // 源文件路径: proj/my-util/util.go
    // 定义包名: util
    package util
    ```
    ```go
    // 导入util包路径
    import "proj/my-util"

    // 引用util包
    util.doSomething()
    ```

- 静态导入，在导入的包路径之前增加一个小数点`.`

    ```go
    // 类似C中的include 或Java中的import static
    import . "fmt"

    // 然后像使用本包元素一样使用fmt包中可见的元素，不需要通过包名引用
    Println("no need package name")
    ```

- 导入包但不直接使用该包，在导入的包路径之前增加一个下划线`_`

    ```go
    // 如果当前go源文件中未引用过log包，将会导致编译错误
    import "log"    // 错误
    import . "log"  // 静态导入未使用同样报错

    // 在包名前面增加下划线表示导入包但是不直接使用它，被导入的包中的init函数会在导入的时候执行
    import _ "github.com/go-sql-driver/mysql"
    ```

### <span id="包内元素的可见性-accessability">**包内元素的可见性 Accessability**</span>

- 名称首字符为[Unicode包含的大写字母](http://www.fileformat.info/info/unicode/category/Lu/list.htm)的元素是被导出的，对外部包是可见的  
  首字为非大写字母的元素只对本包可见(同包跨源文件可以访问，子包不能访问)

    ```go
    var In int                                      // In is exported
    var in byte                                     // in is unexported
    var ȸȹ string                                   // ȸȹ is unexported
    const Ȼom bool = false                          // Ȼom is exported
    const ѧѩ uint8 = 1                             // ѧѩ is unexported
    type Ĩnteger int                                // Ĩnteger is exported
    type ブーリアン *bool                             // ブーリアン is unexported
    func Ӭxport() {...}                              // Ӭxport is exported
    func įnner() {...}                               // įnner is unexported
    func (me *Integer) ⱱalueOf(s string) int {...}   // ⱱalueOf is unexported
    func (i ブーリアン) Ȿtring() string {...}         // Ȿtring is exported
    ```

- internal包（内部包） `Go1.4+`  
  internal包及其子包中的导出元素只能被与internal同父包的其他包访问  

  例如有Go目录如下：  
  `MODULE`  
  　├─x0  
  　│　├─`internal`  
  　│　│　└─z0  
  　│　└─y0  
  　│　　└─z1  
  　└─x1  
  　　└─y1
  
  在x0，y0，z1包中可以访问internal，z0包中的可见元素  
  在x1，y1包中不能导入internal，z0包

- 规范导入包路径Canonical import paths `Go1.4+`  
  包声明语句后面添加标记注释，用于标识这个包的规范导入路径。

    ```go
    package pdf // import "rsc.io/pdf"
    ```

  如果使用此包的代码的导入的路径不是规范路径，go命令会拒绝编译。  
  例如有 [rsc.io/pdf]() 的一个fork路径 [github.com/rsc/pdf]()  
  如下程序代码导入路径时使用了非规范的路径则会被go拒绝编译

    ```go
    import "github.com/rsc/pdf"
    ```

## <span id="数据类型-data-type">**数据类型 Data Type**</span>

### <span id="基础数据类型-basic-data-type">**基础数据类型 Basic data type**</span>

- 基本类型包含：数值类型，布尔类型，字符串

    |类型|取值范围|默认零值|类型|取值范围|默认零值|
    |:-:|:-:|:-:|:-:|:-:|:-:|
    |`int`|**int32,int64**|0|`uint`|**uint32,uint64**|0|
    |`int8`|**-2<sup>7</sup> ~ 2<sup>7</sup>-1**|0|`uint8`,`byte`|0 ~ **2<sup>8</sup>-1**|0|
    |`int16`|**-2<sup>15</sup> ~ 2<sup>15</sup>-1**|0|`uint16`|0 ~ **2<sup>16</sup>-1**|0|
    |`int32`,`rune`|**-2<sup>31</sup> ~ 2<sup>31</sup>-1**|0|`uint32`|0 ~ **2<sup>32</sup>-1**|0|
    |`int64`|**-2<sup>63</sup> ~ 2<sup>63</sup>-1**|0|`uint64`|0 ~ **2<sup>64</sup>-1**|0|
    |`float32`|IEEE-754 32-bit|0.0|`float64`|IEEE-754 64-bit|0.0|
    |`complex64`|**float32+float32**i|0 + 0i|`complex128`|**float64+float64**i|0 + 0i|
    |`bool`|**true,false**|false|`string`|"" ~ "∞"|"",\`\`|
    |`uintptr`|**uint32,uint64**|0|`error`|-|nil|

    > `byte` 是 `uint8` 的别名  
      `rune` 是 `int32` 的别名，代表一个Unicode码点  
      `int`与`int32`或`int64`是不同的类型，只是根据架构对应32/64位值  
      `uint`与`uint32`或`uint64`是不同的类型，只是根据架构对应32/64位值

### <span id="变量-variable">**变量 Variable**</span>

- 变量声明, 使用`var`关键字  
  Go中只能使用`var` **声明**变量，无需显式初始化值

    ```go
    var i int       // i = 0

    var s string    // s = ""    (Go中的string是值类型，默认零值是空串 "" 或 ``，不存在nil(null)值)

    var e error     // e = nil, error是Go的内建接口类型。
    ```
  
  关键字的顺序错误或缺少都是编译错误的

    ```go
    var int a       // 编译错误
    a int           // 编译错误
    int a           // 编译错误
    ```

- `var` 语句可以声明一个变量列表，类型在变量名之后

    ```go
    var a,b,c int   // a = 0, b = 0, c = 0
    var (
        a int       // a = 0
        b string    // b = ""
        c uint      // c = 0
    )
    var (
        a,b,c int
        d string
    )
    ```

- 变量定义时初始化赋值，每个变量对应一个值

    ```go
    var a int = 0
    var a, b int = 0, 1
    ```

- 变量定义并初始化时可以省略类型，Go自动根据初始值推导变量的类型

    ```go
    var a = 'A'         // a int32
    var a,b = 0, "B"    // a int, b string
    ```

- 使用组合符号`:=`定义并初始化变量，根据符号右边表达式的值的类型声明变量并初始化它的值  
  `:=` 不能在函数外使用，函数外的每个语法块都必须以关键字开始

    ```go
    a := 3                     // a int
    a, b, c := 8, '呴', true   // a int, b int32, c bool
    c := `formatted
     string`                   // c string
    c := 1 + 2i                // c complex128
    ```

### <span id="常量-constant">**常量 Constant**</span>

- 常量可以是字符、字符串、布尔或数值类型的值，数值常量是高精度的值

    ```go
    const x int = 3
    const y,z int = 1,2
    const (
        a byte = 'A'
        b string = "B"
        c bool = true
        d int = 4
        e float32 = 5.1
        f complex64 = 6 + 6i
    )
    ```

- 根据常量值自动推导类型

    ```go
    const a = 0        // a int
    const (
        b = 2.3        // b float64
        c = true       // c bool
    )
    ```

- 常量组内定义时复用表达式  
  常量组内定义的常量只有名称时，其值会根据上一次最后出现的常量表达式计算相同的类型与值

    ```go
    const (
        a = 3               // a = 3
        b                   // b = 3
        c                   // c = 3
        d = len("asdf")     // d = 4
        e                   // e = 4
        f                   // f = 4
        g,h,i = 7,8,9       // 复用表达式要一一对应
        x,y,z               // x = 7, y = 8, z = 9
    )
    ```

- 自动递增枚举常量 `iota`  
  iota的枚举值可以赋值给数值兼容类型  
  每个常量单独声明时，`iota`不会自动递增

    ```go
    const a int = iota        // a = 0
    const b int = iota        // b = 0
    const c byte = iota       // c = 0
    const d uint64 = iota     // d = 0
    ```

- 常量组合声明时，`iota`每次引用会逐步自增，初始值为0，步进值为1

    ```go
    const (
        a uint8 = iota        // a = 0
        b int16 = iota        // b = 1
        c rune = iota         // c = 2
        d float64 = iota      // d = 3
        e uintptr = iota      // e = 4
    )
    ```

- 即使`iota`不是在常量组内第一个开始引用，也会按组内常量数量递增

    ```go
    const (
        a = "A"
        b = 'B'
        c = iota    // c = 2
        d = "D"
        e = iota    // e = 4
    )
    ```

- 枚举的常量都为同一类型时，可以使用简单序列格式(组内复用表达式).

    ```go
    const (
        a = iota     // a int32 = 0
        b            // b int32 = 1
        c            // c int32 = 2
    )
    ```

- 枚举序列中的未指定类型的常量会跟随序列前面最后一次出现类型定义的类型

    ```go
    const (
        a byte = iota    // a uint8 = 0
        b                // b uint8 = 1
        c                // c uint8 = 2
        d rune = iota    // d int32 = 3
        e                // e int32 = 4
        f                // f int32 = 5
    )
    ```

- `iota`自增值只在一个常量定义组合中有效，跳出常量组合定义后`iota`初始值归0

    ```go
    const (
        a = iota     // a int32 = 0
        b            // b int32 = 1
        c            // c int32 = 2
    )
    const (
        e = iota     // e int32 = 0    (iota重新初始化并自增)
        f            // f int32 = 1
    )
    ```

- 定制`iota`序列初始值与步进值 (通过组合内复用表达式实现)

    ```go
    const (
        a = (iota + 2) * 3    // a int32 = 6    (a=(0+2)*3) 初始值为6,步进值为3
        b                     // b int32 = 9    (b=(1+2)*3)
        c                     // c int32 = 12    (c=(2+2)*3)
        d                     // d int32 = 15    (d=(3+2)*3)
    )
    ```

### <span id="数组-array">**数组 Array**</span>

- 数组声明带有长度信息且长度固定，数组是值类型默认零值不是`nil`，传递参数时会进行复制。  
  声明定义数组时中括号`[ ]`在类型名称之前，赋值引用元素时中括号`[ ]`在数组变量名之后。

    ```go
    var a [3]int = [3]int{0, 1, 2}                         // a = [0 1 2]
    var b [3]int = [3]int{}                                // b = [0 0 0]
    var c [3]int
    c = [3]int{}
    c = [3]int{0,0,0}                                      // c = [0 0 0]
    d := [3]int{}                                          // d = [0 0 0]
    fmt.Printf("%T\t%#v\t%d\t%d\n", d, d, len(d), cap(d))  // [3]int    [3]int{0, 0, 0}    3    3
    ```
  
  使用`...`自动计算数组的长度

    ```go
    var a = [...]int{0, 1, 2}

    // 多维数组只能自动计算最外围数组长度
    x := [...][3]int{{0, 1, 2}, {3, 4, 5}}
    y := [...][2][2]int{{{0,1},{2,3}},{{4,5},{6,7}}}

    // 通过下标访问数组元素
    println(y[1][1][0])                                    // 6
    ```
  
  初始化指定索引的数组元素，未指定初始化的元素保持默认零值

    ```go
    var a = [3]int{2:3}
    var b = [...]string{2:"c", 3:"d"}
    ```

### <span id="切片-slice">**切片 Slice**</span>
- slice 切片是对一个数组上的连续一段的引用，并且同时包含了长度和容量信息  
  因为是引用类型，所以未初始化时的默认零值是`nil`，长度与容量都是0

    ```go
    var a []int
    fmt.Printf("%T\t%#v\t%d\t%d\n", a, a, len(a), cap(a))    // []int    []int(nil)    0    0

    // 可用类似数组的方式初始化slice
    var d []int = []int{0, 1, 2}
    fmt.Printf("%T\t%#v\t%d\t%d\n", d, d, len(d), cap(d))    // []int    []int{0, 1, 2}    3    3

    var e = []string{2:"c", 3:"d"}
    ```
  
  使用内置函数make初始化slice，第一参数是slice类型，第二参数是长度，第三参数是容量(省略时与长度相同)

    ```go
    var b = make([]int, 0)
    fmt.Printf("%T\t%#v\t%d\t%d\n", b, b, len(b), cap(b))    // []int    []int{}    0    0

    var c = make([]int, 3, 10)
    fmt.Printf("%T\t%#v\t%d\t%d\n", c, c, len(c), cap(c))    // []int    []int{}    3    10

    var a = new([]int)
    fmt.Printf("%T\t%#v\t%d\t%d\n", a, a, len(*a), cap(*a))  // *[]int    &[]int(nil)    0    0
    ```

- 基于slice或数组重新切片，创建一个新的 slice 值指向相同的数组  
  重新切片支持两种格式：  
  
  2个参数 `slice[beginIndex:endIndex]`  
  需要满足条件：0 <= beginIndex <= endIndex <= cap(slice)  
  截取从开始索引到结束索引-1 之间的片段  
  新slice的长度：`length=(endIndex - beginIndex)`  
  新slice的容量：`capacity=(cap(slice) - beginIndex)`  
  beginIndex的值可省略，默认为0  
  endIndex 的值可省略，默认为len(slice)

    ```go
    s := []int{0, 1, 2, 3, 4}
    a := s[1:3]            // a: [1 2],  len: 2,  cap:  4
    b := s[:4]             // b: [0 1 2 3],  len: 4,  cap:  5
    c := s[1:]             // c: [1 2 3 4],  len: 4,  cap:  4
    d := s[1:1]            // d: [],  len: 0,  cap:  4
    e := s[:]              // e: [0 1 2 3 4],  len: 5,  cap:  5
    ```

  3个参数 `slice[beginIndex:endIndex:capIndex]` `Go1.2+`  
  需要满足条件：0 <= beginIndex <= endIndex <= capIndex <= cap(slice)  
  新slice的长度：`length=(endIndex - beginIndex)`  
  新slice的容量：`capacity=(capIndex - beginIndex)`  
  beginIndex的值可省略，默认为0  

    ```go
    s := make([]int, 5, 10)
    a := s[9:10:10]        // a: [0],  len: 1,  cap:  1
    b := s[:3:5]           // b: [0 0 0],  len: 3,  cap:  5
    ```

- 向slice中追加/修改元素

    ```go
    s := []string{}
    s = append(s, "a")              // 添加一个元素
    s = append(s, "b", "c", "d")    // 添加一列元素
    t = []string{"e", "f", "g"}
    s = append(s, t...}             // 添加另一个切片t的所有元素
    s = append(s, t[:2]...}         // 添加另一个切片t的部分元素

    s[0] = "A"                      // 修改切片s的第一个元素
    s[len(s)-1] = "G"               // 修改切片s的最后一个元素
    ```

- 向slice指定位置插入元素，从slice中删除指定的元素  
  因为slice引用指向底层数组，数组的长度不变元素是不能插入/删除的  
  插入的原理就是从插入的位置将切片分为两部分依次将首部、新元素、尾部拼接为一个新的切片  
  删除的原理就是排除待删除元素后用其他元素重新构造一个数组

    ```go
    func insertSlice(s []int, i int, elements ...int) []int {
  	    // x := append(s[:i], append(elements, s[i:]...)...)
  	    x := s[:i]
	    x = append(x, elements...)
        x = append(x, s[i:]...)
	    return x
    }

    func deleteByAppend() {
        i := 3
        s := []int{1, 2, 3, 4, 5, 6, 7}
        // delete the fourth element(index is 3), using append
        s = append(s[:i], s[i+1:]...)
    }

    func deleteByCopy() {
        i := 3
        s := []int{1, 2, 3, 4, 5, 6, 7}
        // delete the fourth element(index is 3), using copy
        copy(s[i:], s[i+1:])
        s = s[:len(s)-1]
    }
    ```

### <span id="字典映射-map">**字典/映射 Map**</span>

- map是引用类型，使用内置函数 `make`进行初始化，未初始化的map零值为 `nil`长度为0，并且不能赋值元素

    ```go
    var m map[int]int
    m[0] = 0                              // × runtime error: assignment to entry in nil map
    fmt.Printf("type: %T\n", m)           // map[int]int
    fmt.Printf("value: %#v\n", m)         // map[int]int(nil)
    fmt.Printf("value: %v\n", m)          // map[]
    fmt.Println("is nil: ", nil == m)     // true
    fmt.Println("length: ", len(m))       // 0，if m is nil, len(m) is zero.
    ```
  
  使用内置函数make初始化map

    ```go
    var m map[int]int = make(map[int]int)
    m[0] = 0                              // 插入或修改元素
    fmt.Printf("type: %T\n", m)           // map[int]int
    fmt.Printf("value: %#v\n", m)         // map[int]int(0:0)
    fmt.Printf("value: %v\n", m)          // map[0:0]
    fmt.Println("is nil: ", nil == m)     // false
    fmt.Println("length: ", len(m))       // 1
    ```
  
  直接赋值初始化map

    ```go
    m := map[int]int{
    0:0,
    1:1,                                  // 最后的逗号是必须的
    }
    n := map[string]S{
    "a":S{0,1},
    "b":{2,3},                            // 类型名称可省略
    }
    ```
  
  map的使用：读取、添加、修改、删除元素

    ```go
    m[0] = 3                              // 修改m中key为0的值为3
    m[4] = 8                              // 添加到m中key为4值为8

    a := n["a"]                           // 获取n中key为“a“的值
    b, ok := n["c"]                       // 取值, 并通过ok(bool)判断key对应的元素是否存在.

    delete(n, "a")                        // 使用内置函数delete删除key为”a“对应的元素.
    ```

### <span id="结构体-struct">**结构体 Struct**</span>

- 结构体类型`struct`是一个字段的集合

    ```go
    type S struct {
        A int
        B, c string
    }
    ```

- 结构体初始化通过结构体字段的值作为列表来新分配一个结构体。

    ```go
    var s S = S{0, "1", "2"}
    ```

- 使用 Name: 语法可以仅列出部分字段(字段名的顺序无关)

    ```go
    var s S = S{B: "1", A: 0}
    ```

- 结构体是值类型，传递时会复制值，其默认零值不是`nil`

    ```go
    var a S
    var b = S{}
    fmt.Println(a == b)    // true
    ```

- 结构体组合  
  将一个`命名类型`作为匿名字段嵌入一个结构体  
  嵌入匿名字段支持命名类型、命名类型的指针和接口类型

    ```go
    package main

    type (
        A struct {
            v int
        }

        // 定义结构体B，嵌入结构体A作为匿名字段
        B struct {
            A
        }

        // 定义结构体C，嵌入结构体A的指针作为匿名字段
        C struct {
            *A
        }
    )

    func (a *A) setV(v int) {
        a.v = v
    }

    func (a A) getV() int {
        return a.v
    }

    func (b B) getV() string {
        return "B"
    }

    func (c *C) getV() bool {
        return true
    }

    func main() {
        a := A{}
        b := B{}    // 初始化结构体B，其内匿名字段A默认零值是A{}
        c := C{&A{}}    // 初始化结构体C，其内匿名指针字段*A默认零值是nil，需要初始化赋值

        println(a.v)

        // 结构体A嵌入B，A内字段自动提升到B
        println(b.v)

        // 结构体指针*A嵌入C，*A对应结构体内字段自动提升到C
        println(c.v)

        a.setV(3)
        b.setV(5)
        c.setV(7)
        println(a.getV(), b.A.getV(), c.A.getV())
        println(a.getV(), b.getV(), c.getV())
    }
    ```

- 匿名结构体  
  匿名结构体声明时省略了`type`关键字，并且没有名称

    ```go
    package main

    import "fmt"

    type Integer int

    // 声明变量a为空的匿名结构体类型
    var a struct{}

    // 声明变量b为包含一个字段的匿名结构体类型
    var b struct{ x int }

    // 声明变量c为包含两个字段的匿名结构体类型
    var c struct {
        u int
        v bool
    }

    func main() {
        printa(a)
        b.x = 1
        fmt.Printf("bx: %#v\n", printb(b))    // bx: struct { y uint8 }{y:0x19}
        printc(c)

        // 声明d为包含3个字段的匿名结构体并初始化部分字段
        d := struct {
            x int
            y complex64
            z string
        }{
            z: "asdf",
            x: 111,
        }
        d.y = 22 + 333i
        fmt.Printf("d: %#v\n", d)    // d: struct { x int; y complex64; z string }{x:111, y:(22+333i), z:"asdf"}

        // 声明变量e为包含两个字段的匿名结构体类型
        // 包含1个匿名结构体类型的命名字段和1个命名类型的匿名字段
        e := struct {
            a struct{ x int }
            // 结构体组合嵌入匿名字段只支持命名类型
            Integer
        }{}
        e.Integer = 444
        fmt.Printf("e: %#v\n", e)    // e: struct { a struct { x int }; main.Integer }{a:struct { x int }{x:0}, Integer:444}
    }

    // 函数参数为匿名结构体类型时，传入参数类型声明必须保持一致
    func printa(s struct{}) {
        fmt.Printf("a: %#v\n", s)    // a: struct {}{}
    }

    // 函数入参和返回值都支持匿名结构体类型
    func printb(s struct{ x int }) (x struct{ y byte }) {
        fmt.Printf("b: %#v\n", s)    // b: struct { x int }{x:1}
        x.y = 25
        return
    }

    func printc(s struct {u int; v bool }) {
        fmt.Printf("c: %#v\n", s)    // c: struct { u int; v bool }{u:0, v:false}
    }
    ```

### <span id="指针-pointer">**指针 Pointer**</span>

- 通过取地址操作符`&`获取指向值/引用对象的指针。

    ```go
    var i int = 1
    pi := &i    // 指向数值的指针

    a := []int{0, 1, 2}
    pa := &a    // 指向引用对象的指针

    var s *S = &S{0, "1", "2"}    // 指向值对象的指针
    ```

- 内置函数`new(T)`分配了一个零初始化的 T 值，并返回指向它的指针

    ```go
    var i = new(int)
    var s *S = new(S)
    ```

- 使用`*`读取/修改指针指向的值

    ```go
    func main() {
        i := new(int)
        *i = 3
        println(i, *i)    // 0xc208031f80    3

        i = new(int)
        println(i, *i)    // 0xc208031f78    0
    }
    ```

- 指针使用点号来访问结构体字段  
  结构体字段/方法可以通过结构体指针来访问，通过指针间接的访问是透明的。

    ```go
    fmt.Println(s.A)
    fmt.Println((*s).A)
    ```

- 指针的指针

    ```go
    func main() {
        var i int
        var p *int
        var pp **int
        var ppp ***int
        var pppp ****int
        println(i, p, pp, ppp, pppp)    // 0 0x0 0x0 0x0 0x0

        i, p, pp, ppp, pppp = 123, &i, &p, &pp, &ppp
        println(i, p, pp, ppp, pppp)    // 123 0xc208031f68 0xc208031f88 0xc208031f80 0xc208031f78
        println(i, *p, **pp, ***ppp, ****pppp)    // 123 123 123 123 123
    }
    ```

- 跨层指针元素的使用  
  在指针引用多层对象时，指针是针对引用表达式的最后一位元素。

    ```go
    package a

    type X struct {
        A Y
    }
    type Y struct {
        B Z
    }
    type Z struct {
        C int
    }
    ```

    ```go
    package main
    import (
        "a"
        "fmt"
    )

    func main() {
        var x = a.X{}
        var p = &x
        fmt.Println("x: ", x)    // x:  {{{0}}}
        println("p: ", p)    // p:  0xc208055f20
        fmt.Println("*p: ", *p)    // *p:  {{{0}}}
        println("x.A.B.C: ", x.A.B.C)    // x.A.B.C:  0
        //  println("*p.A.B.C: ", *p.A.B.C)    // invalid indirect of p.A.B.C (type int)
        println("(*p).A.B.C: ", (*p).A.B.C)    // (*p).A.B.C:  0
    }
    ```

- Go的指针没有指针运算，但是 **道高一尺，魔高一丈**  
  [Go语言中的指针运算](http://1234n.com/?post/rseosp)  
  [利用unsafe操作未导出变量](http://my.oschina.net/goal/blog/193698)

### <span id="通道-channel">**通道 Channel**</span>

- channel用于两个goroutine之间传递指定类型的值来同步运行和通讯。  
  操作符`<-`用于指定channel的方向，发送或接收。  
  如果未指定方向，则为双向channel。

    ```go
    var c0 chan int      // 可用来发送和接收int类型的值
    var c1 chan<- int    // 可用来发送int类型的值
    var c2 <-chan int    // 可用来接收int类型的值
    ```

- channel是引用类型，使用`make`函数来初始化。  
  未初始化的channel零值是`nil`，且不能用于发送和接收值。

    ```go
    c0 := make(chan int)        // 不带缓冲的int类型channel
    c1 := make(chan *int, 10)    // 带缓冲的*int类型指针channel
    ```
  无缓冲的channe中有值时发送方会阻塞，直到接收方从channel中取出值。  
  带缓冲的channel在缓冲区已满时发送方会阻塞，直到接收方从channel中取出值。  
  接收方在channel中无值会一直阻塞。

- 通过channel发送一个值时，`<-`作为二元操作符使用，  

    ```go
    c0 <- 3
    ```

  通过channel接收一个值时，`<-`作为一元操作符使用。

    ```go
    i := <-c1
    ```

- 关闭channel，只能用于双向或只发送类型的channel  
  只能由 **发送方**调用`close`函数来关闭channel  
  接收方取出已关闭的channel中发送的值后，后续再从channel中取值时会以非阻塞的方式立即返回channel传递类型的零值。

    ```go
    ch := make(chan string, 1)

    // 发送方，发送值后关闭channel
    ch <- "hello"
    close(ch)


    // 接收方，取出发送的值
    fmt.Println(<-ch)    // 输出： “hello”

    // 再次从已关闭的channel中取值，返回channel传递类型的零值
    fmt.Println(<-ch)    // 输出： 零值，空字符串“”

    // 接收方判断接收到的零值是由发送方发送的还是关闭channel返回的默认值
    s, ok := <-ch
    if ok {
        fmt.Println("Receive value from sender:", s)
    } else {
        fmt.Println("Get zero value from closed channel")
    }

    // 向已关闭的通道发送值会产生运行时恐慌panic
    ch <- "hi"
    // 再次关闭已经关闭的通道也会产生运行时恐慌panic
    close(ch)
    ```

- 使用`for range`语句依次读取发送到channel的值，直到channel关闭。

    ```go
    package main

    import "fmt"

    func main() {
        // 无缓冲和有缓冲的channel的range用法相同
        var ch = make(chan int)    // make(chan int, 2) 或 make(chan int , 100)
        go func() {
            for i := 0; i < 5; i++ {
                ch <- i
            }
            close(ch)
        }()

        // channel中无发送值且未关闭时会阻塞
        for x := range ch {
            fmt.Println(x)
        }
    }
    ```

  下面方式与for range用法效果相同

    ```go
    loop:
        for {
            select {
            case x, ok := <-c:
                if !ok {
                    break loop
                }
                fmt.Println(x)
            }
        }
    ```

### <span id="接口-interface">**接口 Interface**</span>

- 接口类型是由一组类型定义的集合。  
  接口类型的值可以存放实现这些方法的任何值。

    ```go
    type Abser interface {
        Abs() float64
    }
    ```

- 类型通过实现定义的方法来实现接口， 不需要显式声明实现某接口。

    ```go
    type MyFloat float64

    func (f MyFloat) Abs() float64 {
        if f < 0 {
            return float64(-f)
        }
        return float64(f)
    }
    ```

- 接口组合

    ```go
    type Reader interface {
        Read(b []byte) (n int)
    }

    type Writer interface {
        Write(b []byte) (n int)
    }

    // 接口ReadWriter组合了Reader和Writer两个接口
    type ReadWriter interface {
        Reader
        Writer
    }

    type File struct {
        // ...
    }

    func (f *File) Read(b []byte) (n int) {
        println("Read", len(b),"bytes data.")
        return len(b)
    }

    func (f *File) Write(b []byte) (n int) {
        println("Write", len(b),"bytes data.")
        return len(b)
    }

    func main() {
        // *File 实现了Read方法和Write方法，所以实现了Reader接口和Writer接口以及组合接口ReadWriter
        var f *File = &File{}
        var r Reader = f
        var w Writer = f
        var rw ReadWriter = f
        bs := []byte("asdf")
        r.Read(bs)
        rw.Read(bs)
        w.Write(bs)
        rw.Write(bs)
    }
    ```

- 内置接口类型`error`是一个用于表示错误情况的常规接口，其零值`nil`表示没有错误  
  所有实现了`Error`方法的类型都能表示为一个错误

    ```go
    type error interface {
        Error() string
    }
    ```

### <span id="自定义类型">**自定义类型**</span>

- Go中支持自定义的类型可基于： 基本类型、数组类型、切片类型、字典类型、函数类型、结构体类型、通道类型、接口类型以及自定义类型的类型

    ```go
    type (
        A int
        B int8
        C int16
        D rune
        E int32
        F int64
        G uint
        H byte
        I uint16
        J uint32
        K uint64
        L float32
        M float64
        N complex64
        O complex128
        P uintptr
        Q bool
        R string
        S [3]uint8
        T []complex128
        U map[string]uintptr
        V func(i int) (b bool)
        W struct {a, b int}
        X chan int
        Y any
        Z A
    )
    ```

- 以及支持以上所有支持类型的指针类型

    ```go
    type (
        A *int
        B *int8
        C *int16
        D *rune
        E *int32
        F *int64
        G *uint
        H *byte
        I *uint16
        J *uint32
        K *uint64
        L *float32
        M *float64
        N *complex64
        O *complex128
        P *uintptr
        Q *bool
        R *string
        S *[3]uint8
        T *[]complex128
        U *map[string]uintptr
        V *func(i int) (b bool)
        W *struct {a, b int}
        X *chan int
        Y *any
        Z *A
    )
    ```

- 类型别名 `Go1.9+`

    ```go
    type (
        A struct{}
        B struct{} // 定义两个结构相同的类型A，B
        C = A      // 定义类型A的别名
    )

    func main() {
        var (
            a A
            b B
            c C
        )
        // 因为类型名不同，所以a和b不是相同类型，此处编译错误
        fmt.Println(a == b) // invalid operation: a == b (mismatched types A and B)

        fmt.Println(a == c) // true
        a = C{}
        c = A{}
        fmt.Println(c == a) // true
    }
    ```

- 强制类型转换

  数据类型转换语法规则

    ```go
    // T 为新的数据类型
    newDataTypeVariable = T(oldDataTypeVariable)
    ```

  数值类型转换

    ```go
    var i int = 123
    var f = float64(i)
    var u = uint(f)
    ```

  接口类型转换  
  任意类型的数据都可以转换为其类型已实现的接口类型

    ```go
    type I any    // Go1.18+

    var x int = 123
    var y = I(x)
  
    var s struct{a string}
    var t = I(s)
    ```

  结构体类型转换 `Go1.8+`  
  如果两个结构体包含的所有字段的名称和类型相同(忽略字段的标签差异)，则可以互相强制转换类型。

    ```go
    type T1 struct {
        X int `json:"foo"`
    }

    type T2 struct {
        X int `json:"bar"`
    }

    var v1 = T1{X: 123}
    var v2 = T2(v1)
    ```

## <span id="语句-statement">**语句 Statement**</span>

### <span id="分号括号--">**分号/括号 ; {**</span>

- Go是采用语法解析器自动在每行末尾增加分号，所以在写代码的时候可以省略分号。

- Go编程中只有几个地方需要手工增加分号：  
  for循环使用分号把初始化、条件和遍历元素分开。  
  if/switch的条件判断带有初始化语句时使用分号分开初始化语句与判断语句。  
  在一行中有多条语句时，需要增加分号。

- 控制语句(if，for，switch，select)、函数、方法 的左大括号不能单独放在一行， 语法解析器会在大括号之前自动插入一个分号，导致编译错误。 

### <span id="条件语句-if">**条件语句 if**</span>

- `if`语句 小括号 ( )是可选的，而大括号 { } 是必须的。

    ```go
    if (i < 0)        // 编译错误.
        println(i)

    if i < 0          // 编译错误.
        println(i)

    if (i < 0) {      // 编译通过.
        println(i)
    }

    if (i < 0 || i > 10) {
        println(i)
    }

    if i < 0 {
        println(i)
    } else if i > 5 && i <= 10 {
        println(i)
    } else {
        println(i)
    }
    ```

- 可以在条件之前执行一个简单的语句，由这个语句定义的变量的作用域仅在 if / else if / else 范围之内

    ```go
    if (i := 0; i < 1) {    // 编译错误.
        println(i)
    }

    if i := 0; (i < 1) {    // 编译通过.
        println(i)
    }

    if i := 0; i < 0 {      // 使用gofmt格式化代码会自动移除代码中不必要的小括号( )
        println(i)
    } else if i == 0 {
        println(i)
    } else {
        println(i)
    }
    ```

- `if`语句作用域范围内定义的变量会覆盖外部同名变量，与方法函数内局部变量覆盖全局变量同理

    ```go
    a, b := 0, 1
    if a, b := 3, 4; a > 1 && b > 2 {
        println(a, b)    // 3 4
    }
    println(a, b)        // 0 1
    ```

- `if`判断语句类型断言

    ```go
    package main

    func f0() int {return 333}

    func main() {
        x := 9
        checkType(x)
        checkType(f0)
    }

    func checkType(x any) {    // Go1.18+
        // 断言传入的x为int类型，并获取值
        if i, ok := x.(int); ok {
            println("int: ", i)    // int:  0
        }

        if f, ok := x.(func() int); ok {
            println("func: ", f())    // func:  333
        }

        // 如果传入x类型为int，则可以直接获取其值
        a := x.(int)
        println(a)

        // 如果传入x类型不是byte，则会产生恐慌panic
        b := x.(byte)
        println(b)
    }
    ```

### <span id="分支选择-switch">**分支选择 switch**</span>

- `switch`存在分支选择对象时，`case`分支支持单个常量、常量列表

    ```go
    switch x {
    case 0:
        println("single const")
    case 1, 2, 3:
        println("const list")
    default:
        println("default")
    }
    ```

- 分支选择对象之前可以有一个简单语句，case语句的大括号可以省略

    ```go
    switch x *= 2; x {
    case 4: {
        println("single const")
    }
    case 5, 6, 7: {
        println("const list")
    }
    default: {
        println("default")
    }
    }
    ```

- `switch`只有一个简单语句，没有分支选择对象时，case分支支持逻辑表达式语句

    ```go
    switch x /= 3; {
    case x == 8:
        println("expression")
    case x >= 9:
        println("expression")
    default:
        println("default")
    }
    ```

- `switch`没有简单语句，没有分支选择对象时，case分支支持逻辑表达式语句

    ```go
    switch {
    case x == 10:
        println("expression")
    case x >= 11:
        println("expression")
    default:
        println("default")
    }
    ```

- `switch`类型分支，只能在switch语句中使用的`.(type)`获取对象的类型。

    ```go
    package main

    import (
        "fmt"
        "code.google.com/p/go.crypto/openpgp/errors"
    )

    func main() {
        var (
            a = 0.1
            b = 2+3i
            c = "asdf"
            d = [...]byte{1, 2, 3}
            e = []complex128{1+2i}
            f = map[string]uintptr{"a": 0}
            g = func(int) bool {return true}
            h = struct { a, b int }{}
            i = &struct {}{}
            j chan int
            k chan <- bool
            l <-chan string
            m errors.SignatureError
        )
        // Go1.18+
        values := []any{nil, a, b, &c, d, e, f, g, &g, h, &h, i, j, k, l, m}
        for _, v := range values {
            typeswitch(v)
        }
    }

    func typeswitch(x any) {    // Go1.18+
        // switch x.(type) {    // 不使用类型值时
        switch i := x.(type) {
        case nil:
            fmt.Println("x is nil")
        case int, int8, int16, rune, int64, uint, byte, uint16, uint32, uint64, float32, float64, complex64, complex128, uintptr, bool, string:
            fmt.Printf("basic type : %T\n", i)
        case *int, *int8, *int16, *rune, *int64, *uint, *byte, *uint16, *uint32, *uint64, *float32, *float64, *complex64, *complex128, *uintptr, *bool, *string:
            fmt.Printf("basic pointer type : %T\n", i)
        case [3]byte, []complex128, map[string]uintptr:
            fmt.Printf("collection type : %T\n", i)
        case func(i int) (b bool), *func():
            fmt.Printf("function type : %T\n", i)
        case struct {a, b int}, *struct {}:
            fmt.Printf("struct type : %T\n", i)
        case chan int, chan <- bool, <-chan string:
            fmt.Printf("channel type : %T\n", i)
        case error, interface{a(); b()}:
            fmt.Printf("interface type : %T\n", i)
        default:
            fmt.Printf("other type : %T\n", i)
        }
    }

    // output: 

    // x is nil
    // basic type : float64
    // basic type : complex128
    // basic pointer type : *string
    // collection type : [3]uint8
    // collection type : []complex128
    // collection type : map[string]uintptr
    // function type : func(int) bool
    // other type : *func(int) bool
    // struct type : struct { a int; b int }
    // other type : *struct { a int; b int }
    // struct type : *struct {}
    // channel type : chan int
    // channel type : chan<- bool
    // channel type : <-chan string
    // interface type : errors.SignatureError
    ```

- `switch`中每个case分支默认带有break效果，一个分支执行后就跳出switch，不会自动向下执行其他case。  
  使用`fallthrough`强制向下继续执行后面的case代码。  
  在类型分支中不允许使用`fallthrough`语句

    ```go
    switch {
    case false:
        println("case 1")
        fallthrough
    case true:
        println("case 2")
        fallthrough
    case false:
        println("case 3")
        fallthrough
    case true:
        println("case 4")
    case false:
        println("case 5")
        fallthrough
    default:
        println("default case")
    }
    // 输出：case 2 case 3 case 4
    ```

### <span id="循环语句-for">**循环语句 for**</span>

- Go只有一种循环结构：`for` 循环。  
  可以让前置(初始化)、中间(条件)、后置(迭代)语句为空，或者全为空。

    ```go
    for i := 0; i < 10; i++ {...}
    for i := 0; i < 10; {...}      // 省略迭代语句
    for i := 0; ; i++; {...}       // 省略条件语句
    for ; i < 10; i++ {...}        // 省略初始化语句
    for i := 0; ; {...}            // 省略条件和迭代语句, 分号不能省略
    for ; i < 10; {...}            // 省略初始化和迭代语句, 分号可省略
    for ; ; i++ {...}              // 省略初始化和条件语句, 分号不能省略
    for i < 10 {...}
    for ; ; {...}                  // 分号可省略
    for {...}
    ```

- `for`语句中小括号 ( )是可选的，而大括号 { } 是必须的。

    ```go
    for (i := 0; i < 10; i++) {...}     // 编译错误.
    for i := 0; (i < 10); i++ {...}     // 编译通过.
    for (i < 10) {...}                  // 编译通过.
    ```

- Go的for each循环`for range`

    ```go
    a := [5]int{2, 3, 4, 5, 6}

    for k, v := range a {
        fmt.Println(k, v)    // 输出：0 2, 1 3, 2 4, 3 5, 4 6
    }

    for k := range a {
        fmt.Println(k)    // 输出：0 1 2 3 4
    }

    for _ = range a {
        fmt.Println("print without care about the key and value")
    }
    ```

    `Go1.4+`
    ```go
    for range a {
        fmt.Println("new syntax – print without care about the key and value")
    }
    ```

- 循环的继续、中断、跳转

    ```go
    for k, v := range s {
        if v == 3 {
            continue    // 结束本次循环，进入下一次循环中
        } else if v == 5 {
            break    // 结束整个for循环
        } else {
            goto SOMEWHERE    // 跳转到标签指定的代码处
        }
    }
    ```

- `for range`只支持遍历`数组`、`数组指针`、`slice`、`string`、`map`、`channel`类型

    ```go
    package main
    import "fmt"

    func main() {
        var arr = [...]int{33, 22, 11, 0}
        // 遍历数组，取一位值时为索引值
        for k := range arr {
            fmt.Printf("%d, ", k)   // 0, 1, 2, 3,
        }
        fmt.Println()
        // 遍历数组，取两位值时，第一位为索引值，第二位为元素值
        for k, v := range arr {
            fmt.Printf("%d %d, ", k, v) // 0 33, 1 22, 2 11, 3 0,
        }
        fmt.Println()

        // 遍历数组指针，取一位值时为索引值
        for k := range &arr {
            fmt.Printf("%d, ", k)   // 0, 1, 2, 3,
        }
        fmt.Println()
        // 遍历数组指针，取两位值时，第一位为索引值，第二位为元素值
        for k, v := range &arr {
            fmt.Printf("%d %d, ", k, v) // 0 33, 1 22, 2 11, 3 0,
        }
        fmt.Println()

        var slc = []byte{44, 55, 66, 77}
        // 遍历切片，取一位值时为索引值
        for k := range slc {
            fmt.Printf("%d, ", k)   // 0, 1, 2, 3,
        }
        fmt.Println()
        // 遍历切片，取两位值时，第一位为索引值，第二位为元素值
        for k, v := range slc {
            fmt.Printf("%d %d, ", k, v) // 0 44, 1 55, 2 66, 3 77,
        }
        fmt.Println()

        var str = "abc一二3"
        // 遍历字符串，取一位值时为字节索引值
        for k := range str {
            fmt.Printf("%d, ", k)   // 0, 1, 2, 3, 6, 9,
        }
        fmt.Println()
        // 遍历字符串，取两位值时，第一位为字节索引值，第二位为Unicode字符
        for k, v := range str {
            fmt.Printf("%d %d %s, ", k, v, string(v))   // 0 97 a, 1 98 b, 2 99 c, 3 19968 一, 6 20108 二, 9 51 3,
        }
        fmt.Println()

        var mp = map[int]string{5:"A", 9:"B"}
        // 遍历map，取一位值时为键key
        for k := range mp {
            fmt.Printf("%d, ", k)   // 9, 5,
        }
        fmt.Println()
        // 遍历map，取两位值时，第一位为键key，第二位为元素值value
        for k, v := range mp {
            fmt.Printf("%d %s, ", k, v) // 5 A, 9 B,
        }
        fmt.Println()

        var ch = make(chan int)
        go func() {
            for i := 0; i < 5; i++ {
                ch <- i
            }
            close(ch)
        }()
        // 遍历channel时，只能取一位值，为发送方发送到channel中的值
        for x := range ch {
            fmt.Printf("%d ", x)    // 0 1 2 3 4
        }
    }
    ```

### <span id="通道选择-select">**通道选择 select**</span>

- ` select`用于当前goroutine从一组可能的通讯中选择一个进一步处理。  
  如果任意一个通讯都可以进一步处理，则从中随机选择一个，执行对应的语句。否则在没有默认分支(default case)时，select语句则会阻塞，直到其中一个通讯完成。  
  select 的 case 里的操作语句只能是IO操作

    ```go
    ch1, ch2 := make(chan int), make(chan int)

    // 因为没有值发送到select中的任一case的channel中，此select将会阻塞
    select {
    case <-ch1:
        println("channel 1")
    case <-ch2:
        println("channel 2")
    }
    ```

    ```go
    ch1, ch2 := make(chan int), make(chan int)

    // 因为没有值发送到select中的任一case的channel中，此select将会执行default分支
    select {
    case <-ch1:
        println("channel 1")
    case <-ch2:
        println("channel 2")
    default:
        println("default")
    }
    ```

- select只会执行一次case分支的逻辑，与`for`组合使用实现多次遍历分支

    ```go
    func main() {
        for {
            select {
            case <-time.Tick(time.Second):
                println("Tick")
            case <-time.After(5 * time.Second):
                println("Finish")
            default:
                println("default")
                time.Sleep(5e8)
            }
        }

        ctx, cancel := context.WithTimeout(context.Background(), time.Second)
        defer cancel()
    loop:
        for {
            select {
            case <-ctx.Done():
                println("Tick")
                break loop
            case <-time.After(5 * time.Second):
                println("Finish")
                break loop
            default:
                println("default")
                time.Sleep(5e8)
            }
        }
    }
    ```

### <span id="延迟执行-defer">**延迟执行 defer**</span>

- `defer`语句调用函数，将调用的函数加入defer栈，栈中函数在defer所在的主函数返回时执行，执行顺序是先进后出/后进先出。

    ```go
    package main

    func main() {
        defer print(0)
        defer print(1)
        defer print(2)
        defer print(3)
        defer print(4)

        for i := 5; i <= 9; i++ {
            defer print(i)
        }
        // 输出：9876543210
    }
    ```

- defer在函数返回后执行，可以修改函数返回值

    ```go
    package main

    func main() {
        println(f())    // 返回： 15
    }

    func f() (i int) {
        defer func() {
            i *= 5
        }()
        return 3
    }
    ```

- defer用于释放资源  
  
  释放锁
    ```go
    mu.Lock()
    defer mu.Unlock()
    ```
  
  关闭channel

    ```go
    ch <- "hello"
    defer close(ch)
    ```
  
  关闭IO流

    ```go
    f, err := os.Open("file.xxx")
    defer f.Close()
    ```
  
  关闭数据库连接

    ```go
    db, err := sql.Open("mysql","user:password@tcp(127.0.0.1:3306)/hello")
    if err != nil {
        log.Fatal(err)
    }
    defer db.Close()
    ```

- defer用于恐慌的截获  
  `panic`用于产生恐慌，`recover`用于截获恐慌，recover只能在defer语句中使用, 直接调用recover是无效的。

    ```go
    func main() {
        f()
        fmt.Println("main normal...")
    }

    func f() {
        defer func() {
            if r := recover(); r != nil {
                fmt.Println("catch:", r)
            }
        }()
        p()
        fmt.Println("normal...")
    }

    func p() {
        panic("exception...")
    }
    ```

### <span id="跳转语句-goto">**跳转语句 goto**</span>

- `goto`用于在一个函数内部运行跳转到指定标签的代码处，不能跳转到其他函数中定义的标签。

- `goto`模拟循环

    ```go
    package main

    func main() {
        i := 0
    loop:
        i++
        if i < 5 {
            goto loop
        }
        println(i)
    }
    ```

- `goto`模拟`continue`，`break`

    ```go
    func main() {
        i, sum := 0, 0
    head:
        for ; i <= 10; i++ {
            if i < 5 {
                i++    // 此处必须单独调用一次，因为goto跳转时不会执行for循环的自增语句
                goto head    // continue
            }
            if i > 9 {
                goto tail    // break
            }
            sum += i
        }
    tail:
        println(sum)    // 输出：35
    }
    ```

- **注意**：任何时候都不建议使用`goto`  

### <span id="阻塞语句-blocking">阻塞语句</span>

- 永久阻塞语句

    ```go
    // 向一个未初始化的channel中写入数据会永久阻塞
    (chan int)(nil) <- 0
    // 从一个未初始化的channel中读取数据会永久阻塞
    <-(chan struct{})(nil)
    for range (chan struct{})(nil){}

    // select无任何选择分支会永久阻塞
    select{}
    ```

## <span id="函数-function">**函数 Function**</span>

### <span id="函数声明-declare">**函数声明 Declare**</span>

- 使用关键字`func`声明函数，函数可以没有参数或接受多个参数

    ```go
    func f0() {/*...*/}

    func f1(a int) {/*...*/}

    func f2(a int, b byte) {/*...*/}
    ```

- 在函数参数类型之前使用`...`声明该参数为可变数量的参数  
  可变参数只能声明为函数的最后一个参数。

    ```go
    func f3(a ...int) {/*...*/}

    func f4(a int, b bool, c ...string) {/*...*/}
    ```

- 函数可以返回任意数量的返回值

    ```go
    func f0() {
        return
    }

    func f1() int {
        return 0
    }

    func f2() (int, string) {
        return 0, "A"
    }
    ```

- 函数返回结果参数，可以像变量那样命名和使用

    ```go
    func f() (a int, b string) {
        a = 1
        b = "B"
        return    // 即使return后面没有跟变量，关键字在函数结尾也是必须的
        // 或者 return a, b
    }
    ```

- 当两个或多个连续的函数命名参数是同一类型，则除了最后一个类型之外，其他都可以省略

    ```go
    func f0(a,b,c int) {/*...*/}

    func f1() (a,b,c int) {/*...*/}

    func f2(a,b int, c,d byte) (x,y int, z,s bool) {/*...*/}
    ```

### <span id="函数闭包-closure">**函数闭包 Closure**</span>

- 匿名函数、闭包、函数值  
  Go中函数作为第一类对象，可以作为值对象赋值给变量  
  可以在函数体外/内定义匿名函数，命名函数不能嵌套定义到函数体内，只能定义在函数体外

    ```go
    package main

    type Myfunc func(i int) int

    func f0(name string){
        println(name)
    }

    func main() {
        var a = f0
        a("hello")    // hello

        var f1 Myfunc = func(i int) int {
            return i
        }
        fmt.Println(f1(3))    // 3

        var f2 func() int = func() int {
            return 0
        }
        fmt.Println(f2())     // 0

        // 省略部分关键字
        var f3 func() = func() {/*...*/}
        var f4 = func() {/*...*/}
        f5 := func() {/*...*/}
    }
    ```

### <span id="内建函数-builtin">**内建函数 Builtin**</span>

- `func append`

    ```go
    func append(slice []Type, elems ...Type) []Type
    ```

    > 内建函数append将元素追加到切片的末尾。若它有足够的容量，其目标就会重新切片以容纳新的元素。否则，就会分配一个新的基本数组。append返回更新后的切片，因此必须存储追加后的结果。

    ```go
    slice = append(slice, elem1, elem2)
    slice = append(slice, anotherSlice...)
    ```

    > 作为特例，可以向一个字节切片append字符串，如下：

    ```go
    slice = append([]byte("hello "), "world"...)
    ```
    　  

- `func cap`

    ```go
    func cap(v Type) int
    ```

    > 内建函数cap返回 v 的容量，这取决于具体类型：  
    数组：v中元素的数量，与 len(v) 相同  
    数组指针：*v中元素的数量，与len(v) 相同  
    切片：切片的容量（底层数组的长度）；若 v为nil，cap(v) 即为零  
    信道：按照元素的单元，相应信道缓存的容量；若v为nil，cap(v)即为零

    　  

- `func close`

    ```go
    func close(c chan<- Type)
    ```

    > 内建函数close关闭信道，该通道必须为双向的或只发送的。它应当只由发送者执行，而不应由接收者执行，其效果是在最后发送的值被接收后停止该通道。在最后的值从已关闭的信道中被接收后，任何对其的接收操作都会无阻塞的成功。对于已关闭的信道，语句：

    ```go
    x, ok := <-c    // ok值为false
    ```
    　  

- `func complex`

    ```go
    func complex(r, i FloatType) ComplexType
    ```

    > 使用实部r和虚部i生成一个复数。

    ```go
    c := complex(1, 2)
    fmt.Println(c)    // (1+2i)
    ```
    　  

- `func copy`

    ```go
    func copy(dst, src []Type) int
    ```

    > 内建函数copy将元素从来源切片复制到目标切片中，也能将字节从字符串复制到字节切片中。copy返回被复制的元素数量，它会是 len(src) 和 len(dst) 中较小的那个。来源和目标的底层内存可以重叠。

    ```go
    a, b, c := []byte{1, 2, 3}, make([]byte, 2), 0
    fmt.Println("a:", a, " b:", b, " c: ", c)    // a: [1 2 3]  b: [0 0]  c:  0

    c = copy(b, a)
    fmt.Println("a:", a, " b:", b, " c: ", c)    // a: [1 2 3]  b: [1 2]  c:  2

    b = make([]byte, 5)
    c = copy(b, a)
    fmt.Println("a:", a, " b:", b, " c: ", c)    // a: [1 2 3]  b: [1 2 3 0 0]  c:  3

    s := "ABCD"
    c = copy(b, s)
    fmt.Println("s:", s, " b:", b, " c: ", c)    // s: ABCD  b: [65 66 67 68 0]  c:  4
    ```
    　  

- `func delete`

    ```go
    func delete(m map[Type]Type1, key Type)
    ```

    > 内建函数delete按照指定的键将元素从映射中删除。若m为nil或无此元素，delete不进行操作。

    ```go
    m := map[int]string{
        0: "A",
        1: "B",
        2: "C",
    }
    delete(m, 1)
    fmt.Println(m)    // map[2:C 0:A]

    delete(m, 3)    // 此行代码执行没有任何操作，也不会报错。
    ```
    　  

- `func imag`

    ```go
    func imag(c ComplexType) FloatType
    ```

    > 返回复数c的虚部。

    ```go
    c := 2+5i
    fmt.Println(imag(c))    // 5
    ```
    　  

- `func len`

    ```go
    func len(v Type) int
    ```

    > 内建函数len返回 v 的长度，这取决于具体类型：  
    数组：v中元素的数量  
    数组指针：*v中元素的数量（v为nil时panic）  
    切片、映射：v中元素的数量；若v为nil，len(v)即为零  
    字符串：v中字节的数量，计算字符数量使用`utf8.RuneCountInString()`  
    通道：通道缓存中队列（未读取）元素的数量；若v为 nil，len(v)即为零

    　  

- `func make`

    ```go
    func make(Type, size IntegerType) Type
    ```

    > 内建函数make分配并初始化一个类型为切片、映射、或通道的对象。其第一个实参为类型，而非值。make的返回类型与其参数相同，而非指向它的指针。其具体结果取决于具体的类型：  
    切片：size指定了其长度。该切片的容量等于其长度。切片支持第二个整数实参可用来指定不同的容量；它必须不小于其长度，因此 make([]int, 0, 10) 会分配一个长度为0，容量为10的切片。  
    映射：初始分配的创建取决于size，但产生的映射长度为0。size可以省略，这种情况下就会分配一个小的起始大小。  
    通道：通道的缓存根据指定的缓存容量初始化。若 size为零或被省略，该信道即为无缓存的。

    　  

- `func new`

    ```go
    func new(Type) *Type
    ```

    > 内建函数new分配内存。其第一个实参为类型，而非值。其返回值为指向该类型的新分配的零值的指针。

    　  

- `func panic`

    ```go
    func panic(v any)    // Go1.18+
    ```

    > 内建函数panic停止当前Go程的正常执行。当函数F调用panic时，F的正常执行就会立刻停止。F中defer的所有函数先入后出执行后，F返回给其调用者G。G如同F一样行动，层层返回，直到该Go程中所有函数都按相反的顺序停止执行。之后，程序被终止，而错误情况会被报告，包括引发该恐慌的实参值，此终止序列称为恐慌过程。

    　  

- `func print`

    ```go
    func print(args ...Type)
    ```

    > 内建函数print以特有的方法格式化参数并将结果写入标准错误，用于自举和调试。

    　  

- `func println`

    ```go
    func println(args ...Type)
    ```

    > println类似print，但会在参数输出之间添加空格，输出结束后换行。

    　  

- `func real`

    ```go
    func real(c ComplexType) FloatType
    ```

    > 返回复数c的实部。

    ```go
    c := 2+5i
    fmt.Println(real(c))    // 2
    ```  
    　  

- `func recover`

    ```go
    func recover() any    // Go1.18+
    ```

    > 内建函数recover允许程序管理恐慌过程中的Go程。在defer的函数中，执行recover调用会取回传至panic调用的错误值，恢复正常执行，停止恐慌过程。若recover在defer的函数之外被调用，它将不会停止恐慌过程序列。在此情况下，或当该Go程不在恐慌过程中时，或提供给panic的实参为nil时，recover就会返回nil。

### <span id="初始化函数-init">**初始化函数 init**</span>

- `init`函数是用于程序执行前做包的初始化工作的函数  
  `init`函数的声明没有参数和返回值

    ```go
    func init() {
        // ...
    }
    ```

- 一个package或go源文件可以包含零个或多个init函数

    ```go
    package main

    func main() {
    }

    func init() {
        println("init1...")
    }
    func init() {
        println("init2...")
    }
    func init() {
        println("init3...")
    }
    ```

- init函数被自动调用，在main函数之前执行，不能在其他函数中调用，显式调用会报错该函数未定义。

    ```go
    func init() {
        println("init...")
    }

    func main() {
        init()    // undefined: init
    }
    ```

- 所有`init`函数都会被自动调用，调用顺序如下：
    1. 同一个go文件的init函数调用顺序是 **从上到下**的
    2. 同一个package中按go源文件名字符串比较 **从小到大**顺序调用各文件中的init函数
    3. 不同的package，如果不相互依赖的，按照main包中 **先import的后调用**的顺序调用其包中的init函数
    4. 如果package存在依赖，则先调用最早被依赖的package中的init函数

### <span id="方法-method">**方法 Method**</span>

- 通过指定函数的接收者receiver,将函数绑定到一个类型或类型的指针上,使这个函数成为该类型的方法。  
  只能对命名类型和命名类型的指针编写方法。   
  只能在定义命名类型的那个包编写其方法。  
  不能对接口类型和接口类型的指针编写方法。  
  方法的接收者receiver是类型的值时，编译器会隐式的生成一个同名方法，其接收者receiver为该类型的指针，反过来却不会。

    ```go
    package main

    type A struct {
        x, y int
    }

    // 定义结构体的方法，'_'表示方法内忽略使用结构体、字段及其他方法
    func (_ A) echo_A() {
        println("(_ A)")
    }

    // 同上
    func (A) echoA(s string) {
        println("(A)", s)
    }

    // 定义结构体指针的方法，'_'表示方法内忽略使用结构体指针、字段及其他方法
    func (_ *A) echo_жA() {
        println("(_ *A)")
    }

    // 同上
    func (*A) echoжA(s string) {
        println("(*A)", s)
    }

    // 定义结构体的方法，方法内可以引用结构体、字段及其他方法
    func (a A) setX(x int) {
        a.x = x
    }

    // 定义结构体指针的方法，方法内可以引用结构体、结构体指针、字段及其他方法
    func (a *A) setY(y int) {
        a.y = y
    }

    func main() {
        var a A    // a = A{}
        a.setX(3)
        a.setY(6)
        println(a.x, a.y) // 0  6
        a.echo_A()    // (_ A)
        a.echoA("a")    // (A) a
        a.echo_жA()    // (_ *A)
        a.echoжA("a")    // (*A) a

        // 以下是定义在结构体值上的方法原型，通过调用结构体类型上定义的函数，传入结构体的值
        A.echo_A(a)    // (_ A)
        A.echoA(a, "a")    // (A) a
        // A.echo_жA(a)    // A.echo_жA未定义
        // A.echoжA(a)    //  A.echoжA未定义
        A.setX(a, 4)
        // A.setY(a, 7)    // A.setY未定义
        println(a.x) // 0


        b := &a
        b.setX(2)
        b.setY(5)
        println(b.x, b.y) // 0  5
        b.echo_A()    // (_ A)
        b.echoA("b")    // (A) b
        b.echo_жA()    // (_ *A)
        b.echoжA("b")    // (*A) b

        // 以下是定义在结构体指针上的方法原型，通过调用结构体类型指针上定义的函数，传入结构体的指针
        (*A).echo_A(b)    // (_ A)
        (*A).echoA(b, "b")    // (A) b
        (*A).echo_жA(b)    // (_ *A)
        (*A).echoжA(b, "b")    // (*A) b
        (*A).setX(b, 1)
        (*A).setY(b, 8)
        println(b.x, b.y)    // 0   8

        // 调用结构体空指针上的方法，以下注释掉的代码都是空指针错误
        var c *A    // c = nil
        // c.setX(2)
        // c.setY(5)
        // println(c.x, c.y)
        // c.echo_A()
        // c.echoA()
        c.echo_жA()    // (_ *A)
        c.echoжA("c")    // (*A) c

        // (*A).echo_A(c)
        // (*A).echoA(c)
        (*A).echo_жA(c)    // (_ *A)
        (*A).echoжA(c, "c")    // (*A) c
        // (*A).setX(c, 1)
        // (*A).setY(c, 8)
        // println(c.x, c.y)
    }
    ```

- 结构体中组合匿名字段时，匿名字段的方法会向外传递，其规则如下：   
  匿名字段为值类型时：值的方法会传递给结构体的值，指针的方法会传递给结构体的指针；   
  匿名字段为指针类型时：指针的方法会传递给值和指针；   
  匿名字段为接口类型时：方法会传递给值和指针； 

- Go中有匿名函数，但是没有匿名方法

## <span id="并发-concurrency">**并发 Concurrency**</span>

- 协程`goroutine`是由Go运行时环境管理的轻量级线程。  
  使用关键字`go`调用一个函数/方法，启动一个新的协程goroutine

    ```go
    package main

    import (
        "time"
    )

    func say(i int) {
        println("goroutine:", i)
    }

    func main() {
        for i := 1; i <= 5; i++ {
            go say(i)
        }
        say(0)
        time.Sleep(5 * time.Second)
    }
    ```

    主协程goroutine输出0，其他由go启动的几个子协程分别输出1～5

    > goroutine: 0  
    goroutine: 1  
    goroutine: 2  
    goroutine: 3  
    goroutine: 4  
    goroutine: 5

- goroutine 在相同的地址空间中运行，因此访问共享内存必须进行同步。

    ```go
    package main

    import (
        "sync"
        "time"
    )

    var mu sync.Mutex
    var i int

    func main() {
        for range [5]byte{} {
            go Add()
        }
        time.Sleep(5*time.Second)
        println(i)
    }

    func Add() {
        // 使用互斥锁防止多个协程goroutine同时修改共享变量
        // 只能限制同时访问此方法修改变量，在方法外修改则限制是无效的
        mu.Lock()
        defer mu.Unlock()
        i++
    }
    ```

  使用通道channel进行同步

    ```go
    package main

    import (
        "time"
    )

    var i int
    var ch = make(chan byte, 1)

    func main() {
        for range [5]byte{} {
            go Add()
        }
        time.Sleep(5*time.Second)
        println(i)
    }

    func Add() {
        ch <- 0
        i++
        <-ch
    }
    ```

- 使用channel在不同的goroutine之间通信

    ```go
    // 上一个例子只是将channel用作同步开关，稍做修改即可在不同goroutine间通信
    package main

    import (
        "time"
    )

    var i int
    var ch = make(chan int, 1)

    func main() {
        for range [5]byte{} {
            go Add()
        }
        ch <- i
        time.Sleep(5*time.Second)
        i = <-ch
        println(i)
    }

    func Add() {
        // 从channel中接收的值是来自其他goroutine发送的
        x := <-ch
        x++
        ch <- x
    }
    ```

## <span id="测试-testing">**测试 Testing**</span>

- Go中自带轻量级的测试框架testing和自带的go test命令来实现单元测试和基准测试

### <span id="单元测试-unit">**单元测试 Unit**</span>

- 有如下待测试unittest包，一段简单的求和代码

    ```go
    package testgo

    import "math"

    func Sum(min, max int) (sum int) {
        if min < 0 || max < 0 || max > math.MaxInt32 || min > max {
            return 0
        }

        for ; min <= max; min++ {
            sum += min
        }
        return
    }
    ```

- 测试源文件名必须是`_test.go`结尾的，go test的时候才会执行到相应的代码  
  必须import testing包  
  所有的测试用例函数必须以`Test`开头  
  测试用例按照源码中编写的顺序依次执行  
  测试函数TestXxx()的参数是`*testing.T`，可以使用该类型来记录错误或者是测试状态  
  测试格式：`func TestXxx (t *testing.T)`，Xxx部分可以为任意的字母数字的组合，首字母不能是小写字母[a-z]，例如Testsum是错误的函数名。  
  函数中通过调用*testing.T的Error，Errorf，FailNow，Fatal，FatalIf方法标注测试不通过，调用Log方法用来记录测试的信息。

    ```go
    package testgo

    import "testing"

    func TestSum(t *testing.T) {
        s := Sum(1, 0)
        t.Log("Sum 1 to 0:", s)
        if 0 != s {
            t.Error("not equal.")
        }
        s = Sum(1, 10)
        t.Log("Sum 1 to 10:", s)
        if 55 != s {
            t.Error("not equal.")
        }
    }
    ```

  在当前包中执行测试：`go test -v`

  > === RUN TestSum  
  --- PASS: TestSum (0.00s)  
        t0_test.go:7: Sum 1 to 0: 0  
        t0_test.go:12: Sum 1 to 10: 55  
  PASS  
  ok      /home/cxy/go/code/unittest        0.004s

### <span id="基准测试-benchmark">**基准测试 Benchmark**</span>

- 基准测试 Benchmark用来检测函数/方法的性能  
  基准测试用例函数必须以`Benchmark`开头  
  go test默认不会执行基准测试的函数，需要加上参数-test.bench  
  语法:-test.bench="test_name_regex"，例如go test -test.bench=".*"表示测试全部的基准测试函数  
  在基准测试用例中，在循环体内使用testing.B.N，使测试可以正常的运行

    ```go
    package testgo

    import "testing"

    func BenchmarkSum(b *testing.B) {
        b.Logf("Sum 1 to %d: %d\n", b.N, Sum(1, b.N))
    }
    ```

  在当前包中执行测试：`go test -v -bench .`

  > BenchmarkSum    2000000000               0.91 ns/op  
  --- BENCH: BenchmarkSum  
        t0_test.go:19: Sum 1 to 1: 1  
        t0_test.go:19: Sum 1 to 100: 5050  
        t0_test.go:19: Sum 1 to 10000: 50005000  
        t0_test.go:19: Sum 1 to 1000000: 500000500000  
        t0_test.go:19: Sum 1 to 100000000: 5000000050000000  
        t0_test.go:19: Sum 1 to 2000000000: 2000000001000000000  
  ok      /home/cxy/go/code/benchmark        1.922s

### <span id="模糊测试-fuzzing">**模糊测试 Fuzzing**</span>

- 模糊测试针对测试运行输入随机数据，尝试找出漏洞或导致崩溃的输入  
  可以通过模糊测试发现的一些漏洞示例包括 SQL 注入、缓冲区溢出、拒绝服务和跨站点脚本攻击。

    ```go
    // 待测试函数
    func Reverse(s string) string {
        b := []byte(s)
        for i, j := 0, len(b)-1; i < len(b)/2; i, j = i+1, j-1 {
            b[i], b[j] = b[j], b[i]
        }
        return string(b)
    }
    ```

  模糊测试用例函数以`Fuzz`开头

    ```go
    func FuzzReverse(f *testing.F) {
        testcases := []string{"Hello, world", " ", "!12345"}
            for _, tc := range testcases {f.Add(tc) // Use f.Add to provide a seed corpus
        }
        f.Fuzz(func(t *testing.T, orig string) {
            rev := Reverse(orig)
            doubleRev := Reverse(rev)
            if orig != doubleRev {
                t.Errorf("Before: %q, after: %q", orig, doubleRev)
            }
            if utf8.ValidString(orig) && !utf8.ValidString(rev) {
                t.Errorf("Reverse produced invalid UTF-8 string %q", rev)
            }
        })
    }
    ```

  在当前包中执行测试：`go test -fuzz FuzzReverse`

  > fuzz: elapsed: 0s, gathering baseline coverage: 0/11 completed  
    failure while testing seed corpus entry: FuzzReverse/91f92b2e5d60254ba0d9f6ed565497fc550f81fe2c93c55c9a00034ffc3bceaf  
    fuzz: elapsed: 0s, gathering baseline coverage: 3/11 completed  
    --- FAIL: FuzzReverse (0.03s)  
    --- FAIL: FuzzReverse (0.00s)  
    hi_test.go:59: Reverse produced invalid UTF-8 string "\x81\xc7"  
  > 
  > FAIL  
    exit status 1  
    FAIL    /home/cxy/go/code/fuzz 0.176s

## <span id="泛型-generics">**泛型 Generics**</span> `Go1.18+`

### <span id="类型约束-constraint">**类型约束 type constraint**</span>

- 定义约束指定类型`T`的`类型约束`接口

    ```go
    type I10 interface {
        int
    }

    type I11 interface {
        string
    }
    ```

    ```go
    type S struct {
        b bool
        e error
    }

    type I12 interface {
        S
    }
    ```

- 定义约束近似类型`~T`的`类型约束`接口

    ```go
    type I20 interface {
        ~int
    }

    type I21 interface {
        ~string
    }
    ```

    约束近似类型的限制:

    - 近似类型`~T`必须是底层类型自身, 不能是基于底层类型自定义的新类型  
      在[自定义类型](#自定义类型)小节中所有自定义的`A~Z`类型都被限制

        ```go
        type I22 interface {
            ~A    // 错误: A的底层类型是 int
            ~B    // 错误: B的底层类型是 int8
            ~C    // 错误: C的底层类型是 int16
            ~D    // 错误: D的底层类型是 rune
            ~E    // 错误: E的底层类型是 int32
            ~F    // 错误: F的底层类型是 int64
            ~G    // 错误: G的底层类型是 uint
            ~H    // 错误: H的底层类型是 byte
            ~I    // 错误: I的底层类型是 uint16
            ~J    // 错误: J的底层类型是 uint32
            ~K    // 错误: K的底层类型是 uint64
            ~L    // 错误: L的底层类型是 float32
            ~M    // 错误: M的底层类型是 float64
            ~N    // 错误: N的底层类型是 complex64
            ~O    // 错误: O的底层类型是 complex128
            ~P    // 错误: P的底层类型是 uintptr
            ~Q    // 错误: Q的底层类型是 bool
            ~R    // 错误: R的底层类型是 string
            ~S    // 错误: S的底层类型是 [3]uint8
            ~T    // 错误: T的底层类型是 []complex128
            ~U    // 错误: U的底层类型是 map[string]uintptr
            ~V    // 错误: V的底层类型是 func(i int) (b bool)
            ~W    // 错误: W的底层类型是 struct {a, b int}
            ~X    // 错误: X的底层类型是 chan int
            ~Y    // 错误: Y的底层类型是 any
            ~Z    // 错误: Z的底层类型是 int
        }
 
        // 正确的近似类型
        type I23 interface {
            ~int
            ~int8
            ~int16
            ~rune
            ~int32
            ~int64
            ~uint
            ~byte
            ~uint16
            ~uint32
            ~uint64
            ~float32
            ~float64
            ~complex64
            ~complex128
            ~uintptr
            ~bool
            ~string
            ~[3]uint8
            ~[]complex128
            ~map[string]uintptr
            ~func(i int) (b bool)
            ~struct {a, b int}
            ~chan int
        }
        ```

    - 近似类型`~T`不能是接口类型

        ```go
        type I24 interface {
            ~error    // 错误: 不能是接口类型
        }
        ```

- 定义约束联合类型`A|B|C|~D`的`类型约束`接口

    ```go
    type I30 interface {
        int
        string
    }

    type I31 interface {
        ~int
        ~string
    }

    type I32 interface {
        int
        string
        ~int
        ~string
    }

    type Signed interface {
        ~int | ~int8 | ~int16 | ~int32 | ~int64
    }

    type Unsigned interface {
        ~uint | ~uint8 | ~uint16 | ~uint32 | ~uint64 | ~uintptr
    }

    type Integer interface {
        Signed | Unsigned
    }

    type Number interface {
	    Integer
	    String() string
    }
    ```

- `类型约束`接口的限制

    - 命名或匿名的`类型约束`接口只能用于`类型参数`声明

    - 不能用于全局/局部变量、函数/方法形参以及结构体字段的声明

        ```go
        var i0 interface{ int }  // 错误

        func Fn0() {
            var i1 constraints.Integer  // 错误
        }

        func Fn1(i2 interface{ ~int }) { }  // 错误

        var i3 struct{ F0 ~int }  // 错误
        var i4 struct{ F1 constraints.Integer }  // 错误
        ```

### <span id="类型参数-parameters">**类型参数 type parameters**</span>

- `类型参数`声明在函数名与函数参数左括号之间, 为包含在`[]`中的一个或多个类型和参数

    ```go
    // 为函数声明类型参数T, 其类型为any
    func ZeroValue[T any]() {
        var x T
        fmt.Printf("zero value of %T type: %v\n", x, x)
    }

    func main() {
        ZeroValue[bool]()           // zero value of bool type: false
        ZeroValue[complex64]()      // zero value of complex64 type: (0+0i)
        ZeroValue[[3]int]()         // zero value of [3]int type: [0 0 0]
        ZeroValue[map[string]int]() // zero value of map[string]int type: map[]
        ZeroValue[struct {
            b bool
            e error
        }]()                        // zero value of struct { b bool; e error } type: {false <nil>}
    }
    ```

    `类型参数`作为函数输入参数或返回值的类型

    ```go
    func Fn0[T, U any](t T, u U) {
        fmt.Println(t, u)
    }

    func Fn1[T constraints.Integer]() (t T) {
        return T(rand.Int()) // 使用类型参数强制转换类型
    }

    func Fn2[K comparable, V any](m map[K]V) {
        fmt.Println(m)
    }

    func main() {
        Fn0[int, int](123, 456)                // 123 456
        Fn0[string, string]("hello", "world")  // hello
        fmt.Println(Fn1[int64]())              // 5577006791947779410
        Fn2[int, int](map[int]int{1: 2, 3: 4}) // map[1:2 3:4]

        // 函数调用省略类型参数实参[...], Go自动推导其类型
        Fn0(789, "xyz")                             // 789 xyz
        Fn2(map[string]bool{"x": true, "y": false}) // map[x:true y:false]

        // 无法推导类型的场景不能省略类型参数实参
        fmt.Println(Fn1[uint64]()) // 8674665223082153551
    }
    ```

- `类型参数`声明在类型名右边, 为包含在`[]`中的一个或多个类型和参数

    ```go
    type S[T any] struct {
        Field T
    }

    func (s *S[T])Set(t T) {
        s.Field = t
    }

    func (s *S[T]) Get() (t T) {
        return s.Field
    }

    func main() {
        var s0 = new(S[int])
        // s0.Field = 123
        s0.Set(456)
        fmt.Printf("%#v\n", s0)
 
        s1 := S[string]{
            Field: "hello",
        }
        fmt.Printf("%#v\n", s1)
        fmt.Printf("%#v\n", s1.Get())
    }
    ```

- `类型参数`的类型支持指定类型、近似类型、联合类型、匿名和命名的`类型约束`接口

    ```go
    func Fn10[T int, U uint]()         { /* ... */ }
    func Fn11[T ~string]()             { /* ... */ }
    func Fn12[T ~int | string]()       { /* ... */ }
    func Fn13[T interface{ int }]()    { /* ... */ }
    func Fn14[T constraints.Integer]() { /* ... */ }
    ```

- 基于`类型参数`定义`类型约束`接口

    ```go
    type Slice[T any] interface {
        ~[]T
    }

    type Map[K comparable, V any] interface {
        ~map[K]V
    }

    type Chan[T any] interface {
        ~chan T
    }
    ```

- `类型参数`的限制

    - 不能将`类型参数`作为约束的类型

        ```go
        func Fn0[T any, U T]() { }                      // 错误
        func Fn1[T any, U []T]() { }                    // 正确，约束的类型是[]T
        func Fn2[T comparable, U any, V map[T]U]() { }  // 正确，约束的类型是map[T]U
        ```

    - 不能联合或嵌入`类型参数`和`comparable`作为约束的类型

        ```go
        func Fn0[T any, U int | T]() { }                  // 错误
        func Fn1[T any, U interface{ T }]() { }           // 错误
        func Fn2[T any, U interface{ T | string }]() { }  // 错误
        func Fn3[T comparable | int]() { }                // 错误
        func Fn4[T interface{ comparable }]() { }         // 错误
        ```

    - 联合的约束类型不能存在交集

        ```go
        func Fn0[T int | ~int]() { }               // 错误
        func Fn1[T interface{ int | ~int }]() { }  // 错误

        type Str string
        func Fn2[T string | Str]() { }   // 正确，Str和string是两个不同类型没有交集
        func Fn3[T string | ~Str]() { }  // 错误，~Str是string的近似类型存在交集string

        func Fn4[T byte | uint8]() { }   // 错误，byte和uint8是相同的类型
        ```

    - 联合的约束类型不能使用含有方法的接口类型

        ```go
        func Fn0[T error | int]() { }                         // 错误，error接口含有方法
        func Fn1[T interface{ string | io.Reader }]() { }     // 错误
        func Fn2[T interface{ io.Reader | io.Writer }]() { }  // 错误
        ```
