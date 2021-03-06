# Linux develop tools

## gcc

### 选项

gcc 具备预处理(preprocessing)，编译(compilation)，汇编(assembly)和链接(linking)的功能。通过给定选项(option)可以让结果停留在这中间的任意一步。例如：

| command+option       | output result | 说明                                            |
| -------------------- | ------------- | ----------------------------------------------- |
| gcc -E test.c        | test.i        | 对源程序做预处理，主要是宏展开等操作            |
| gcc -S test.c        | test.S        | 对程序做编译，产生汇编代码                      |
| gcc -c test.c        | test.o        | 对程序做汇编，生成机器码                        |
| gcc test.c [-o test] | test          | 链接，生成可执行文件，如果不指明，默认生成a.out |

需要注意的是，-o本质上是一个重命名选项。无论有没有-o选项，最后都会执行链接的步骤。当不使用-o选项时，执行命令`gcc test.c`，生成的是默认的a.out文件。

不同的选项可以作用于不同的阶段，一些选项可以控制处理器，一些可以控制编译器(compiler)，还有一些选项可以作用于汇编器和链接器。

### 语言支持

绝大部分选项都支持C语言程序，当一些选项只对其他语言（通常是C++）有效时，会在帮助文档中显示说明。如果文档中没有提到程序所用的语言，那就说明这个选项对所有支持的语言都有效。

### 使用

最常见的使用GCC的方式是直接调用可执行程序gcc，或者在交叉编译时调用machine-gcc（这里的machine指的是具体的硬件架构如armeabi），或者通过调用machine-gcc-version(这里的version指的是具体的版本号)来运行一个指定版本的gcc。当需要编译C++程序时，需要调用g++。

gcc接受指定选项，并且接受文件名作为操作对象(operands)。gcc的很多的选项都是多个字母(multi-letter)，因此多个单字母选项集合的选项给定形式是不支持的。例如-dv选项和-d -v选项是完全不同的。

gcc支持指定多种选项，对于大部分情况，指定选项的顺序是没有影响的。但是当使用同类选项多次时，顺序是有影响的。例如：当多次指定-L选项时，gcc会按照指定时的先后顺序去搜索文件夹路径。同样-l选项的放置位置也是有重要影响的。

很多的选项都是以-f或者-W开头的多字母的长名称，例如-fmove-loop-invariants，-Wformat等等。这些选项通常都有正向（positive）和反向（negative）两种相对的选项形式，例如-ffoo的方向形式选项就是-fno-foo。在帮助文档中只给出这两种形式的其中一种说明，给出说明的选项为非默认的那一种选项。

一些选项会带一个或者多个参数（arguments），这些参数之间通常用一个空格或者等于号（=）与选项名之间分隔。除了显示的在文档中说明，一个参数可以为数字或者字符串。数字参数通常是一个小的无符号十进制或者十六进制整数。如果是十六进制整数参数必须以0x作为前缀。对于一些给定大小限制的参数可以任意的大的十进制或者十六进制的整数并以一个字节大小单位作为后缀，例如“kB","KiB"表示千字节，“MB”，“MiB"表示兆字节等等。

### 常用选项

| option          | description                                                  |
| --------------- | ------------------------------------------------------------ |
| -g              | 显示调试信息                                                 |
| -O0/-O1/-O2/-O3 | 指定编译器优化等级。-O0表示没有优化，-O1为默认值，-O3优化级别最高 |
| -Ldir           | 指定编译时搜索库的路径，dir为指定的库搜索路径，如果没有该选项，编译器只会搜索标准库的目录 |
| -ldir           | 指定头文件搜索路径，dir为指定的头文件搜索路径，指定了该选项后，编译器会先到-l指定的目录dir下查找头文件，查找不到时再到系统默认的头文件目录查找。 |
| -llibrary       | 指定编译时使用的库，library为指定使用的库。例如gcc -lcurses test.c 表示使用curses库去编译。 |



## gdb

### 功能

调试器（debugger）的目的是为了让调试者能够看到一个程序执行时程序内部发生的事情。gdb主要提供以下四种功能以帮助调试者定位bug：

- 开始运行你想要调试的程序，并且指定任何可能会影响程序行为的选项。
- 让你的的程序在指定的条件下停止运行。
- 当你的程序停止运行时，测试程序中发生了什么。
- 改变你的程序中的内容，这样就可以让调试者实验对bug的修正。

### 语言支持

gdb可以支持调试C, C++, Fortran和Modula-2语言编写的程序。

### 使用

通过在shell中调用gdb命令来调用gdb，调用之后，gdb会一直从终端（Terminal）中读取命令，直到你输入quit命令退出。在gdb环境中可以使用help命令获取在线帮助。

可以不带任何选项和参数的在shell中运行gdb，进入gdb环境。更常用的用法是通过指定一个可执行程序作为gdb的参数：`gdb program`

还可以同时指定一个可执行程序和core file：`gdb program core`

或者指定一个进程ID作为第二个参数：`gdb program 1234`

上面这个命令会将gdb绑定到进程1234。

通过-p选项还可以直接通过进程ID进入调试，而不需要可执行文件名：

`gdb -p 1234`

在gdb环境下有以下常用指令：

| COMMANDS                             | FUNCTION                                                  |
| ------------------------------------ | --------------------------------------------------------- |
| break [<u>file</u>:] <u>function</u> | 在（file）function处设置一个断点                          |
| run [<u>arglist</u>]                 | 开始运行程序（如果指定arglist，arglist将作为参数）        |
| bt Backtrace                         | 显示程序栈                                                |
| print <u>expr</u>                    | 显示expr表达式的值                                        |
| c                                    | 继续运行程序                                              |
| next                                 | 执行下一行程序，会跳过（step over）这一行的函数调用       |
| step                                 | 执行下一行程序，会进入到（step into）这一行的函数调用内部 |
| edit [<u>file</u>:]<u>function</u>   | 将程序定位到目前停在的地方                                |
| list [<u>file</u>:]<u>function</u>   | 打印程序中目前停止位置附件的文本                          |
| help [<u>name</u>]                   | 显示name所代表的gdb命令的帮助信息                         |
| quit                                 | 退出gdb环境                                               |

## Makefile



## kconfig

