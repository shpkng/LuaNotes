# Lua笔记

​		Part of the power of Lua comes from its libraries. This is not by chance. After all, one of the main strengths of Lua is its extensibility. Many features contribute to this strength. Dynamic typing allows a great degree of polymorphism. Automatic memory management simplifies interfaces, because there is no need to de- cide who is responsible for allocating and deallocating memory or how to handle overflows. First-class functions allow a high degree of parameterization, making functions more versatile. 



**Lua数组默认起点是1**



## The Basics

### Cpt1 Getting Started

#### 代码块

- Lua也可以用来作为数据描述语言 因此数MB的代码块也是不少见的
- 在交互式窗口中，默认每一行都是一个完整的代码块；但是如果编译器认为当前行不完整，就会继续读取接下来的内容
- 可以用 *-i* 选项使Lua在运行指定代码块之后进入交互模式
- 交互编程模式下执行的dofile()函数会立即执行一个文件

#### 代码规范

- 合法的变量名以字母或下划线开头，避免使用**下划线开头并接续以一个或多个大写字母的变量名**，因为它们在Lua中为特殊用途保留。
- 单行注释以**'--'**开头，多行注释有多种形式，其中的一种是**'--[['**和**']]'**结对标记的注释，另一种是**'--[['**和**'--]]'**结对标注的形式。第二种在使用上有一个特点，即我们可以在注释首行的首位再加一个短横，形成**'---[['**，这时注释块内的语句将会被执行。
- Lua的语句之间没有不需要分隔符，但是我们也有使用分号分隔符的自由。换行对Lua的语句没有任何效用.

#### 全局变量

- 全局变量无需声明，对于未初始化的全局变量的引用并非错误，并且会返回**nil**值。
- 将某一全局变量赋值为**nil**，Lua将表现为该变量从未存在，并回收该变量使用的内存。

#### 类型和值

p21

x = x or v

Another useful idiom is ((a and b) or c) or simply (a and b or c) (given that **and** has a higher precedence than **or**). It is equivalent to the C expression a ? b : c, provided that b is not false. For instance, we can select the maximum of two numbers x and y with the expression (x > y) and x or y. When x > y, the first expression of the **and** is true, so the **and** results in its second operand (x), which is always true (because it is a number), and then the **or** expression results in the value of its first operand, x. When x > y is false, the **and** expression is false and so the **or** results in its second operand, y. 



左连接是个啥



## Cpt2 The Eight-Queen Puzzle

回溯+递归

## Cpt3 Numbers

5.2及以前的版本中，Lua只有一种数字类型，即双精度浮点数；从5.3版本开始，Lua内置64位整型和双精度浮点数(称为float)。对于特定平台，Lua 5.3可被编译为使用32位整型和单精度浮点数的Small Lua。

- 通常float和integer都视为number，在特定情况下可以利用math.type()来判断其具体类型
- 数值相等的float和integer被视为相等
- 十六进制数，可在小数部分后加后缀'p'或'P'，如0xa.bp2，其值为0xa.b * 2^2
- 可以利用形如string.format("%a",419)的函数将数字转写为pn形式
- Lua 5.3中实现了向下取整的整除，以及取余，其中取余的实际逻辑为a % b == a - ((a // b) * b)
- 不等运算符：~=。Lua认为任意类型不相同的变量不等。

### 数学类库

​	Lua提供了包括三角函数、对数、伪随机等数学相关资源的数学类库。

#### （伪）随机数生成器

有三种调用方式：

- 无参数调用：返回一个[0,1)区间内的实数
- 单整型参数n：返回一个在[1,n]区间内的整型
- 双整型参数l, u：返回一个在[l,u]区间内的整型

​	我们可以通过math.randomseed(t)来设置随机种子，当我们未指定随机种子时，运行时系统自动将种子设置为1，这将会导致每一次运行该程序时，我们得到同样的随机结果序列。

#### 取整函数

- modf以0为取整方向，并返回两个值，分别为运算后的整数部分和小数部分
- 可以利用floor(x+0.5)的写法实现四舍五入，但是这样的处理在面对极大的整数时会出现不可预测的错误。因此，在这样的场景下我们需要对整数进行单独处理（判断输入值是否为整数）
- 这里还有一个关于奇偶数的问题，书33页

#### 数字极限

- 运算结果溢出整型上下界时，结果会出现wrap

#### 格式转换

#### 运算符优先级

#### 整型出现之前的Lua

#### 强制转换

## Cpt4 字符串

- 字符串类默认处理对象是单字节字符，但是对Unicode进行了适配
- 类似Raw String的表示方式"[[","]]"，在可能由于字符串内"]]"引起匹配错误时，可以使用"[===["和"]===]"标记
- 字符串处理函数不改变输入字符串本身，而是返回一个新的字符串
- string.byte(s, 1, -1)可以返回字符串内所有字符的code，该函数不适用于Unicode字符
- string.char(c)将c转换为字符
- string.format使用方式类似C中的printf
- string.gsub("string", charToSubstitute, substitutionChar) => "result" subCount
- string.format和string.rep中，存在'%c'指令时，不能正常处理Unicode字符

#### Unicode

- Lua内置对UTF-8的支持，但是Lua的操作系统类库和IO类库的Unicode支持受物理主机操作系统的影响，比如，Windows系统使用UTF-16编码的文件名，我们需要额外的支持库才能正常操作它们。
- Codepoint: 码位或编码位置
- Lua内置utf8库
- utf8.len返回字符串的长度，当字符串内出现非法字符时，返回nil和出现错误的位置
- utf8.char类同string.char，utf8.codepoint类同string.byte
- **一个没看懂的地方书44页utf8.offset**
- utf8.codes遍历字符串并返回序号和codepointp，序号是以字节记的字符起始位置
- 















