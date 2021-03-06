> 笔记来源：无闻

# 可见性规则

go 语言中使用大小写来决定常量，变量，类型，接口，结构或函数是否可以被外部函数调用。
根据约定，函数首字母小写即为 private
函数首字母大写即为 public

# go 基本类型

## 布尔型 bool
长度：1字节
取值范围：true，false
注意事项：不可用数字代表 true 或 false

## 整形：int/unit8
根据运行平台可能为32或64位

8位整形：int8/unit8
长度：1字节
取值范围：-128～127/0-255

## 字节型：byte（unit8别名）

### 16位整形：int16/unit16
长度：2字节
取值范围：-32768～32767/0-65535

### 32位整形：int32(rune)/unit32
长度：4字节
取值范围：-2^32/2~2^32/2-1/0~2^32-1

### 64位整形：int64(rune)/unit64
长度：4字节
取值范围：-2^64/2~2^64/2-1/0~2^64-1

### 浮点型：float32/float64
长度：4/8字节
小数位：精确到7/15小数位

### 复数：complex64/complex128
长度：8/16 字节
足够保存指针的32位或64位整数型：uintptr

## 其他值类型：
array,struct,string

## 引用类型：
slice,map,chan

### 接口类型：inteface

### 函数类型：func

###  类型零值
零值并不等于空值，而是变量被声明位某种类型后的默认值，通常为0，bool为false，string为空字符串

# 变量

## 变量的声明与赋值

变量声明格式：var 变量名称 变量类型

变量赋值格式：变量名称 = 表达式

声明的同时赋值：var 变量名称 变量类型 = 表达式

## 多个变量的声明与赋值

全局变量的声明可使用 var() 的方式进行简写

全局变量的声明不可以省略 var ，但可以使用并行方式

所有变量都可以使用类型推断

局部变量不可以使用 var() 的简写方式，只能用并行方式

## 变量的类型转换

go 不存在隐式转换，所有类型转换必须显示

转换只能发生在两种互相兼容的类型之间(整型和布尔型之间不可以转换)

## 常量的定义

常量的右侧必须是常量或常量的表达式

常量表达式中的函数必须是内置函数
> 常量最好使用全部大写

## 常量的初始化规则与枚举
在定义常量组时，如果不提供初始值，则表示将使用上行的表达式
使用相同的表达式不代表具有相同的值
iota 是常量的计数器，从0开始，组中每定义1个常量自动递增1
通过初始化规则与 iota 可以达到枚举效果
每遇到一个 const 关键字，iota 就会重置为0

# 数组

数组的长度也是类型的一部分，因此具有不同长度的数组为不同的类型

注意区分指向数组的指针和指针数组

数组在GO 中为值类型

数组之间可以使用 == 或 != 进行比较，但不可以使用 < 或 >

可以使用 new 来创建数组，此方法返回一个指向数组的指针

GO 支持多维数组

# 切片

## 切片 slice
本身不是数组，它指向底层的数组

作为变长数组的替代方案，可以关联底层数组的局部或全部为引用类型

可以直接创建或从底层数组获取生成

使用len()获取元素个数，cap()获取容量

一般使用make()创建

如果多个clice 指向相同底层数组，其中一个值改变会影响全部

## Reslice
Reslice 时索引以被slice 的切片为准

索引不可以超过被slice 的切片容量cap()值

索引越界不会导致底层数组的重新分配二十引发错误

## Append
可以在slice 尾部追加元素

可以将一个slice 追加在另一个slice 尾部

如果最终长度未超过追加到slice 的容量则返回slice

如果超过追加到slice 的容量则将重新分配数组并拷贝原始数据

# map key-value

类似其他语言中的哈希表或字典，以 key-value 形式存储数据

key 必须是支持 == 或 ！= 比较远算符的类型，不可以是函数，map，或 slice

map 查找比线性都多快，单比使用索引访问数据的类型慢100倍

map 使用 make()创建，支持 := 这种简写

超出容量会自动扩容，但是尽量提供一个合理的初始值

键值对不存在时，自动添加，使用 delete() 删除某些键值对

使用 for range 对 map 和slice 进行迭代操作

# 函数
go 函数不支持嵌套，重载和默认参数

但支持：无需声明原型，不定长度变参，多返回值，命名返回值参数，匿名函数，闭包

定义函数使用 func 且左大括号不能另起一行

函数也可以作为一种类型使用

defer

- defer的执行方式类似其他语言中的析构函数，在函数体执行结束之后按照调用顺序的相反顺序逐个执行
- 即使函数发生严重错误也会执行
- 支持匿名函数的调用
- 常用于资源清理，文件关闭、解锁以及记录时间等操作
- 通常与匿名函数配合可以在 return 之后修改函数计算结果
- 如果函数体内某个变量作为 defer 时匿名函数的参数，则在定义 defer 时即已经获得了拷贝，否则是引用某个变量的地址
- go 没有异常机制，但有 painc/recover 模式来处理错误
- Panic 可以在任何地方引发，但 recover 只有在 defer 调用的函数中有效


# 结构体

go 中的 struct 与 c 中的 struct 很相似，并且 go 没有 class

使用 type <Name> struct{} 定义结构，名称遵循可见性规则

支持指向自身的指针类型成员

支持匿名结构，可用作成员或定义成员变量

匿名结构也可用于 map 的值

可以使用字面值对结构进行初始化

允许直接通过指针来读写结构成员

相同类型的成员可进行直接拷贝赋值

支持 == 与 ！= 比较运算符，但不支持 > 和 <

支持匿名字段，本质上是定义了以某个类型名为名称的字段

嵌入结构作为匿名字段看起来像继承，但不是继承

可以使用匿名字段指针

# 方法

和函数区别是必须声明recriver

go 中虽然没有 class，但依旧有 method

通过显示说明 recriver 来实现与某个类型的组合

只能为同一个包中的类型定义方法

receiver 可以是类型的值或者指针

不存在方法重载

可以使用值或指针来调用方法，编译器会自动完成转换

从某种意义上来说，方法是函数的语法糖，因为 receiver 其实就是方法所接收的第一个参数

如果外部结构和嵌入存在同名方法则有限调用外部结构的方法

类型别名不会拥有底层类型所附带的方法

方法可以调用结构中的非公开字段

# 接口

接口是一个或多个方法签名的集合

只要某个类型拥有该接口的所有方法签名，即算实现该接口，无需显示声明了哪个接口，这称为 Structural Typing

接口只有方法声明，没有实现，没有数据字段

接口可以匿名嵌入其他接口，或嵌入到结构中

将对象赋值给接口时，会发生拷贝，而接口内部存储的是指向这个复制品封指针，既无法修改复制品的状态，页无法获取指针

只有当接口存储的类型和对象都为 nil 时，接口才等于 nil

接口调用不会做  receiver 的自动转换

接口同样支持匿名字段方法

接口可实现类似 OOP 中的多态

空接口可以作为任何数据类型的容器

# 反射

反射可以大大提高程序的灵活性，使得 interface{} 有更大的发挥余地

反射使用 TypeOf 和 ValueOf 函数从接口中获取目标对象信息

反射会将你命字段作为独立字段

想要利用反色号修改对象状态，前提是 interface 是 settable,即 pointer-interface

通过反射可以动态调用方法

# 并发

goroutine 是由官方实现的超级 线程池，每个实例4-5 kb的栈内存占用和由于实现机制而大幅减少的创建和销毁开销，是制造go 号称高并发的根本原因

并发不是并行

- 并发主要由切换时间片来实现同时运行，在并行则是直接利用多核实现多线程的运行，但 go 可以设置使用核数，以发挥多核计算机的能力

goroutine 奉行通过通信来共享内存，而不是共享内存来通信

Channel

- Channel 是 goroutine 沟通的桥梁，大都是阻塞同步的
- 通过 make 创建，close 关闭
- Channel 是引用类型
- 可以使用 for range 来迭代不断操作 channel
- 可以设置单向或双向通道
- 可以设置缓存大小，在未被填满前不会发生阻塞

Select

- 可处理一个或多个 channel 的发送和接收
- 同时由多个可用的 channel 时按随机顺序处理
- 可用空的 select 来阻塞 main 函数
- 可设置超时