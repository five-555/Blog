---
title: C/C++中gcc/g++的参数详解
categories: 技术研究
date: 2024-03-02 22:16:10
tags: [C++, GCC, G++, 编译]
cover:
top_img:
---
## C/C++中gcc/g++的参数详解

> 参考链接：https://www.runoob.com/w3cnote/gcc-parameter-detail.html

GCC（GNU Compiler Collection）和G++分别是GNU（自由和开放源码的操作系统）的C和C++编译器。在执行编译的过程中，总共需要执行4步

* 预处理：预处理器（cpp：C Preprocessor）根据以字符`#`开头的命令，修改原始的C程序，即将头文件作为当前文件的内容插入到程序文本当中。得到另一个C程序，生成的文件为`.i`文件
* 编译：编译器（ccl）将预处理后的文本文件文件`.i`翻译成文本文件`.s`文件，即汇编语言
* 汇编：汇编器（as）将汇编语言翻译成能够供机器识别的机器指令，并保存在`.o`目标文件中，这是二进制文件
* 链接：链接器负责链接目标代码，生成可执行文件，如：main中调用的一个函数，则会把那个函数进行链接

### 基本编译参数

- `-o <file>`: 指定输出的可执行文件名。如果不使用该参数，默认的输出文件名为`a.out`。
- `-c`: 只编译并生成目标文件（`.o`文件），不进行链接。
- `-E`: 只进行预处理，不进行编译、汇编和链接。
- `-S`: 只进行预处理和编译，不进行汇编和链接，生成汇编代码。
- `-x  language`: 设置文件所使用的语言，对后缀名无效
- `-pipe`: 使用管道代替编译中的临时文件
- `include file`: 包含某个文件代码
- `imacros file`: 将file文件的宏，拓展到gcc/g++的输入文件，宏定义本身不出现在输入文件中
- `Dmacro`: 定义宏
- `-C`: 预处理时，不删除注释信息

### 调试和优化信息

- `-g`: 生成调试信息。可以用GDB等调试器调试程序。
- `-O<level>`: 设置优化级别。`<level>`可以是0、1、2、3，其中`-O0`表示不进行优化，`-O2`和`-O3`表示更高级别的优化。
- `-Og`: 优化生成的代码，但不会干扰调试。

### 警告控制

- `-Wall`: 打开几乎所有的警告。
- `-Wextra`: 打开额外的警告。
- `-Werror`: 把所有的警告当作错误。

### 链接库和路径

- `-l<library>`: 链接一个库。例如，使用`-lm`来链接数学库`libm.so`。
- `-L<directory>`: 添加库文件搜索的目录。
- `-I<directory>`: 添加头文件搜索的目录。

- `-static`: 使用静态链接，不使用动态链接库。编译出来的东西，一般都很大。
- `-fPIC`: 生成位置无关代码，通常用于创建共享库。
- `-share`: 尽量使用动态库，生成的文件会较小，但需要系统有动态库。

### 预处理器选项

- `-D<macro>`: 定义宏。例如，`-DDEBUG`会定义宏`DEBUG`。
- `-U<macro>`: 取消宏的定义。

### 其他选项

| 选项         | 解释                                                         |
| :----------- | :----------------------------------------------------------- |
| -ansi        | 只支持 ANSI 标准的 C 语法。这一选项将禁止 GNU C 的某些特色， 例如 asm 或 typeof 关键词。 |
| -c           | 只编译并生成目标文件。                                       |
| -DMACRO      | 以字符串"1"定义 MACRO 宏。                                   |
| -DMACRO=DEFN | 以字符串"DEFN"定义 MACRO 宏。                                |
| -E           | 只运行 C 预编译器。                                          |
| -g           | 生成调试信息。GNU 调试器可利用该信息。                       |
| -IDIRECTORY  | 指定额外的头文件搜索路径DIRECTORY。                          |
| -LDIRECTORY  | 指定额外的函数库搜索路径DIRECTORY。                          |
| -lLIBRARY    | 连接时搜索指定的函数库LIBRARY。                              |
| -m486        | 针对 486 进行代码优化。                                      |
| -o FILE      | 生成指定的输出文件。用在生成可执行文件时。                   |
| -O0          | 不进行优化处理。                                             |
| -O 或 -O1    | 优化生成代码。                                               |
| -O2          | 进一步优化。                                                 |
| -O3          | 比 -O2 更进一步优化，包括 inline 函数。                      |
| -shared      | 生成共享目标文件。通常用在建立共享库时。                     |
| -static      | 禁止使用共享连接。                                           |
| -UMACRO      | 取消对 MACRO 宏的定义。                                      |
| -w           | 不生成任何警告信息。                                         |
| -Wall        | 生成所有警告信息。                                           |

