---
title: 大数碎胸口：Golang 的高精度运算
published: 2025-08-15T16:50:09.103Z
description: ''
updated: ''
tags:
  - 算法
  - 胡言乱语
  - Golang
draft: true
pin: 0
toc: true
lang: ''
abbrlink: 'high-accuracy-algorithm-in-golang'
---

先看一道洛谷上的橙题：[P1009 [NOIP 1998 普及组] 阶乘之和 - 洛谷](https://www.luogu.com.cn/problem/P1009)。

![image-20250816011413863](https://gcore.jsdelivr.net/gh/karlbaey/tutu@master/pictures/image-20250816011413863.png)

我最早是通过 Golang 默认的 `int64` 类型写的这道题，并且没有看到 `n` 的范围。根据 Golang 文档，`int64` 最多只支持 $ [-2^{63}, 2^{63}-1] $[^1]区间内的整数，但是仅仅是 `30!` 就达到了可怕的 33 位，所以我们不可能在这里使用普通的数据类型，而应该想一个办法实现大数运算，也就是高精度算法。

> 注：事实上 Golang 提供了 `math/big` 用于计算这种特别大的数，但是可能是因为洛谷 ban 掉了这种神器还是别的什么，只要使用了这个这个包就必定抛出 `Compile Error`。所以我们只能老老实实地手动实现。

本题的 Golang 解法并不难，只把代码贴上即可。

```go
package main

import (
    "fmt"
    "strconv"
)

type bigInt []int

func (big bigInt) String() string {
    var s string
    for i := len(big)-1; i >= 0; i-- {
        s += strconv.Itoa(big[i])
    }
    return s
}

func add(big1, big2 bigInt) bigInt {
    maxLen := len(big1)
    if maxLen < len(big2) {
        maxLen = len(big2)
    }

    res := make(bigInt, maxLen)
    var carry int

    for i := 0; i < maxLen; i++ {
        sum := carry
        if i < len(big1) {
            sum += big1[i]
        }
        if i < len(big2) {
            sum += big2[i]
        }
        res[i] = sum % 10
        carry = sum / 10
    }

    if carry > 0 {
        res = append(res, carry)
    }

    return res
}

func mul(bigNum bigInt, x int) bigInt {
    res := make(bigInt, len(bigNum))
    copy(res, bigNum)

    carry := 0

    for i := 0; i < len(bigNum); i++ {
        product := carry + res[i]*x
        res[i] = product%10
        carry = product/10
    }

    for carry > 0 {
        res = append(res, carry%10)
        carry /= 10
    }

    return res
}

func main() {
    var n int
    fmt.Scan(&n)

    sum := bigInt{0}
    factor := bigInt{1}

    for i := 1; i <= n; i++ {
        factor = mul(factor, i)
        sum = add(sum, factor)
    }

    fmt.Println(sum)
}
```

那么，我们就系统地说一说，在 Golang 中如何实现高精度算法。[^2]

## 基础存储 & 辅助函数

如果要存储大数，那么 `int` 类型是不可能考虑的了，我们只能使用字符串或是数组这种不限长度的数据类型来存储高精度数据，而又考虑到我们希望操作方便，所以这里我们选用数组存储，输出时使用字符串。

```go
package main

import (
	"fmt"
    "strconv"
)

type bigInt []int // 使用数组存储高精度数据

func (big bigInt) String() string {
    var s string
    for i := len(big)-1; i >= 0; i-- { // 反向存储，方便运算
        s += strconv.Itoa(big[i])
    }
    return s
}
```

我们在第一段代码的第 8 行定义了高精度数据的储存方式，如果不调用 `Stringer` 接口，评测机就会输出数组状态下的高精度数据，这显然不是我们想要的。例如，我们计算 1 到 30 每个整数的阶乘之和，输出会像这样：

```out
[3 1 3 0 4 9 0 8 7 3 0 7 9 0 2 4 3 1 2 4 1 0 7 4 8 1 8 0 1 4 4 7 2]
```

 `Stringer` 相当于一个字符串转换器——任何东西只要能用 `String()` 方法把自己转成字符串表示，就是 `Stringer`。`Stringer` 接口的定义如下所示。

```go
type Stringer interface {
    String() string
}
```

我们需要把数组变成字符串，这就是为什么需要 `fmt.Stringer` 接口，也就是代码中的 `String() string` 方法。

## 实现加法和乘法

`bigInt` 存储数据的顺序是与我们平常读数字的顺序相反的，也就是，在 `bigInt` 越靠前，它在原数字中的位数就越低。

加法中，从个位开始计算，如果计算得到的结果大于 10，就应该将结果除以 10 取余数，并加在十位，以此类推。当计算到最高位而进位还有剩余时，就应该在结果数组的末尾处加上进位。我们使用 `res` 存储输出，使用 `carry` 存储进位。代码实现如下所示。

```go
func add(big1, big2 bigInt) bigInt {
    maxLen := len(big1)
    if maxLen < len(big2) {
        maxLen = len(big2)
    }

    res := make(bigInt, maxLen)
    var carry int

    for i := 0; i < maxLen; i++ {
        sum := carry
        if i < len(big1) {
            sum += big1[i] // 加上第一个数
        }
        if i < len(big2) {
            sum += big2[i] // 加上第二个数
        }
        res[i] = sum % 10 // 将 sum mod 10 存储到 res 的对应位中
        carry = sum / 10 // Golang 自动向零取整
    }

    if carry > 0 { // 处理剩余进位
    	res = append(res, carry)
    }
    
    return res
}
```

而计算乘法时，就需要用大数的每一位乘上另一个数，将得到的数的个位（体现在代码中就是 `res[i] = product%10`）存到对应位置中。其余的思想和加法运算是类似的，此处不赘述。

但是在计算前，一定要先把大数复制一份到结果中。与 C++ 的 `<vector>` 不同，Go 的切片是共用底层数组的。如果直接使用 `res := bigNum`，那么当我们修改 `res` 中的数据时，就会一起修改 `bigNum` 的数据。为了避免这种错误，将 `bigNum` 复制一份给 `res` 更加保险。

```go
func mul(bigNum bigInt, x int) bigInt {
    res := make(bigInt, len(bigNum))
    copy(res, bigNum) // 一定要复制

    carry := 0

    for i := 0; i < len(bigNum); i++ {
        product := carry + res[i]*x
        res[i] = product%10
        carry = product/10
    }

    for carry > 0 {
        res = append(res, carry%10)
        carry /= 10
    }

    return res
}
```



[^1]: 我帮你数过了，如果不算负号，两个端点都是 19 位。
[^2]: 因为 Golang 讲究显式，所以下面的具体代码实现对于习惯了 C++ 和 Python 的读者来说可能不太适应，请见谅。
