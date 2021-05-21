---
title: "Notebook 4 Advanced Go Programming Book"
date: 2021-05-10T09:03:14+08:00
draft: false

tags: [
    "notebook",
]

categories: [
	"notebook",
	"Go",
]
---

《GO语言高级编程》 学习笔记

<!--more-->

> [《GO语言高级编程》](https://github.com/chai2010/advanced-go-programming-book)

# CGO

# GO汇编

### 变量

> 变量标志: DUPOK、RODATA和NOPTR
>
> DUPOK: 表示该变量对应的标识符可能有多个，在链接时只选择其中一个即可（一般用于合并相同的常量字符串，减少重复数据占用的空间）
>
> NOPTR: 此变量的内部不含指针数据
>
> RODATA: read only data

- 常量

> 常量以$美元符号为前缀


```
$1           // 十进制
$0xf4f8fcff  // 十六进制
$1.5         // 浮点数
$'a'         // 字符
$"abcd"      // 字符串

$2+2         // 常量表达式
$3&1<<2      // == $4
$(3&1)<<2    // == $4
```

- string
```go
// main.go

package main

import (
	"fmt"
	"....../gs"
)

func main ()  {
	fmt.Println(gs.Helloworld)
	fmt.Println(gs.Name)
}
```

```go
// gs/gs.go

package gs

var Helloworld string
var Name string
```

```
// gs/gs_amd64.s

#include "textflag.h"
GLOBL text<>(SB),NOPTR,$16             // <> 当前文件内的私有变量
DATA text<>+0(SB)/8,$"Hello Wo"
DATA text<>+8(SB)/8,$"rld!"
GLOBL ·Helloworld(SB),RODATA,$16
DATA ·Helloworld+0(SB)/8,$text<>(SB)
DATA ·Helloworld+8(SB)/8,$12

GLOBL ·Name(SB),RODATA,$24
DATA ·Name+0(SB)/8,$·Name+16(SB)
DATA ·Name+8(SB)/8,$6
DATA ·Name+16(SB)/8,$"gopher"
```

- slice

```
var NameByte []byte
```

```
GLOBL ·NameType(SB),$24            // var NameType []byte("Hello World!")
DATA ·NameType+0(SB)/8,$txt<>(SB)  // StringHeader.Data
DATA ·NameType+8(SB)/8,$12         // StringHeader.Len
DATA ·NameType+16(SB)/8,$16        // StringHeader.Cap

GLOBL txt<>(SB),NOPTR,$16
DATA txt<>+0(SB)/8,$"Hello Wo"
DATA txt<>+8(SB)/8,$"rld!"
```

#### 注：调用时为什么是空切片。。。
![call asm slice](/images/call-go-asm-slice.png)
![asm slice error](/images/go-asm-slice.png)

- map、channel

> map/channel等类型并没有公开的内部结构，它们只是一种未知类型的指针，无法直接初始化

```go
var m map[string]int

var ch chan int
```

```
GLOBL ·m(SB),$8  // var m map[string]int
DATA  ·m+0(SB)/8,$0

GLOBL ·ch(SB),$8 // var ch chan int
DATA  ·ch+0(SB)/8,$0
```

### 函数

> TEXT     symbol(SB), [flags,]          $framesize  [-argsize]
>
> TEXT指令、函数名、      可选的flags标志、  函数帧大小和 可选的函数参数大小
>
> NOSPLIT不会生成或包含栈分裂代码，这一般用于没有任何其它函数调用的叶子函数，这样可以适当提高性能。
>
> WRAPPER标志则表示这个是一个包装函数，在panic或runtime.caller等某些处理函数帧的地方不会增加函数帧计数。
>
> NEEDCTXT表示需要一个上下文参数，一般用于闭包函数

```
// func Swap(a, b int) (ret0, ret1 int)
TEXT ·Swap(SB), NOSPLIT, $0-32

// symbol    => Swap     // 
// flags     => NOSPLIT  // 
// framesize => 0        // 局部变量需要多少栈空间 & 调用其它函数时准备调用参数的隐式栈空间
// argsize   => 32       // 参数大小（int ==> int8,所以参数和返回值一共4个）
```

> ![asm func](/images/go-asm-func.png)

- 参数&返回值

> Go汇编中引入FP伪寄存器，表示函数当前帧的地址，也就是第一个参数的地址
>
> 通过+0(FP)、+8(FP)、+16(FP)和+24(FP)来分别引用a、b、ret0和ret1四个参数
> 
> ```
> TEXT ·Swap(SB), $0
>     MOVQ a+0(FP), AX     // AX = a
>     MOVQ b+8(FP), BX     // BX = b
>     MOVQ BX, ret0+16(FP) // ret0 = BX
>     MOVQ AX, ret1+24(FP) // ret1 = AX
>     RET
> ```

- 复杂的函数参数和返回值

```
func Foo(a bool, b int16) (c []byte)

↓		↓		↓

type Foo_args struct {
    a bool
    b int16
    c []byte
}
type Foo_returns struct {
    c []byte
}

↓		↓		↓

func Foo(FP *SomeFunc_args, FP_ret *SomeFunc_returns)
```

> ```
> TEXT ·Foo(SB), $0
>     MOVEQ a+0(FP),       AX // a
>     MOVEQ b+2(FP),       BX // b
>     MOVEQ c_dat+8*1(FP), CX // c.Data
>     MOVEQ c_len+8*2(FP), DX // c.Len
>     MOVEQ c_cap+8*3(FP), DI // c.Cap
>     RET
> ```

- 局部变量

> 通过伪SP来定位局部变量
> 
> ```
> func Foo() {
>     var c []byte
>     var b int16
>     var a bool
> }
> ```
> 
> ```
> TEXT ·Foo(SB), $32-0
>     MOVQ a-32(SP),      AX // a
>     MOVQ b-30(SP),      BX // b
>     MOVQ c_data-24(SP), CX // c.Data
>     MOVQ c_len-16(SP),  DX // c.Len
>     MOVQ c_cap-8(SP),   DI // c.Cap
>     RET
> ```







