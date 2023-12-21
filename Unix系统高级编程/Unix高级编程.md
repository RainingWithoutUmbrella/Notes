# Unix系统高级编程

* 业务逻辑：根据业务需求，按照设计好的逻辑规则，处理信息，与系统无关。
* 系统访问：利用操作系统所提供的各种功能辅助业务逻辑的实现。
* 标准函数：scanf/printf - 源代码级兼容
* 系统函数：read/write - 接口级兼容
* 环境、性能、功能

## 一、Unix系统简介

### 1.1 Unix系统的背景

**1961-1969：史前时代**
CTSS(Compatible Time-Sharing System，兼容分时系统)，以MIT为首的开发小组，小而简单的实验室原型。
Multics(Multiplexed Information and Computing System，多路信息与计算系统)，庞大而负责，不堪重负。
Unics(Uniplexed information and Computing System，单路信息与计算系统)，返璞归真，走上正道。
**1969-1971：创世纪**
Ken Thompson，肯.汤普逊，Unix之父，B语言之父，内核用B语言+汇编语言开发，PDP-7，第一个Unix系统核心和简单应用。后来被移植到PDP-11平台，功能更加完善。
**1971-1979：出谷纪**
Dennis Ritchie，丹尼斯.里奇，C语言之父，用C语言重写了Unix系统内核，极大地提升了Unix系统的可读性、可维护性和可移植性——Unix V7，第一个真正意义上的Unix系统。
**1980-1985：第一次Unix战争**
AT&T贝尔实验室：SVR4
加州大学伯克利分校：BSD+TCP/IP
DARPA，ARPANET(INTERNET)
IEEE，国际电气电子工程师协会，POSIX为Unix内核和外壳制定了一系列技术标准和规范，消除系统版本之间分歧，大一统的操作系统。
**1988-1990：第二次Unix战争**
AT&T+Sun
IBM+DEC+HP
比尔.盖茨->Windows
**1990-现在**
1991，Linus Torvalds创建了Linux系统的内核
1993，Linux已达到产品级操作系统的水准
1993，AT&T将Unix系统卖给Novell
1994，Novell将Unix系统卖给X/Open组织
1995，X/Open将Unix系统捐给SCO
2000，SCO将Unix系统卖给Celdear——Linux发行商
Linux就是现代版本的Unix。

### 1.2 Linux系统的背景

类Unix操作系统，免费开源。
不同发行版本使用相同的内核。
支持多种硬件平台：手机、路由器、视频游戏控制器、个人电脑、大型计算机等等。
隶属于GNU工程。GNU = GNU Not Unix。
受GPL许可证限制：如果发布了可执行的二进制代码，就必须同时发布可读的源代码，并且在发布任何基于GPL许可证的软件时，不能添加任何限制性条款。

### 1.3 Linux系统的版本

早期版本：0.01，0.02，...，1.00
旧计划：1.0.1，...，2.6.0 (A.B.C)
A - 主版本号，内核大幅更新
B - 次版本号，内核重大修改，奇数测试版，偶数稳定版
C - 补丁序号，内核轻微修改
新计划：A.B.C-D.E
D - 构建次数，反映极微小的更新
E - 描述信息
      rc/r - 候选版本
      smp - 支持对称多处理器
      EL - Rad Hat的企业版本
      mm - 试验新技术
      ...
cat /proc/version

### 1.4 Linux系统的特点

遵循GNU/GPL许可证
开放性
多用户
多任务
设备无关性
丰富网络功能
可靠的系统安全
良好的可移植性
### 1.5 Linux的发行版本
Ubuntu - 大众化，简单易用
Linux Mint - 新潮前位
Fedora - Red Hat的桌面版本
openSUSE - 华丽
Debian - 自由开放
Slackware - 朴素简洁，简陋
Red Hat - 经典，稳定，企业应用，支持全面

## 二、GNU编译器(gcc)

### 2.1 GCC的基本特点

**1)**支持多种硬件架构
x86-64
Alpha
ARM
PowerPC
SPARC
VAX
...
**2)**支持多种操作系统
Unix
Linux
BSD
Android
Mac OS X
iOS
Windows
**3)**支持多种编程语言
C
C++
Objective-C
Java
Fortran
Pascal
Ada
**4)**GCC的版本
gcc -v

### 2.2 构建过程

**源代码(.c)-预编译->头文件和宏扩展-编译->汇编码(.s)-汇编->目标码(.o)-链接->可执行代码(a.out)**
代码：hello.c

```c
#include <stdio.h>
int main(void) {
    printf("Hello, World!\n");
    return 0;
}
```

vi hello.c - 编写源代码
gcc -E hello.c -o hello.i - 预编译(编译预处理)
gcc -S hello.i - 获得汇编代码(hello.s)
gcc -c hello.s - 获得目标代码(hello.o)
gcc hello.o -o hello - 获得可执行代码(hello)
./hello - 运行可执行代码

### 2.3 文件名后缀 

**可读文本**

> .h - C语言源代码头文件 
> .c - 预处理前的C语言源代码文件 
> .s - 汇编语言文件    

**不可读的二进制**

> .o - 目标文件 
> .a - 静态库文件
> .so - 共享(动态)库文件 
> .out - 可执行文件 

### 2.4 编译选项

gcc [选项] [参数] 文件1 文件2 ...
**-o: 指定输出文件**
如：gcc hello.c -o hello
**-E: 预编译，缺省输出到屏幕，用-o指定输出文件**
如：gcc -E hello.c -o hello.i
**-S: 编译，将高级语言文件编译成汇编语言文件**
如：gcc -S hello.c
**-c: 汇编，将汇编语言文件汇编成机器语言文件**
如：gcc -c hello.s
**-Wall：产生全部警告**
如：gcc -Wall wall.c
**-Werror：将警告作为错误处理**
如：gcc -Werror werror.c
**-x: 指定源代码的语言**
xxx.c - C语言
xxx.cpp - C++语言
xxx.for - Fortran语言
xxx.java - Java语言
...
gcc -x c++ cpp.c -lstdc++ -o cpp
代码：cpp.c
**-O0/O1/O2/O3: 指定优化等级，O0不优化，缺省O1优化**

### 2.5 头文件

**1)头文件里写什么？**

**头文件卫士(防止重定义,重声明)**

```c
#ifndef __XXX_
#define __XXX_
...
#endif
     a.h
     /  \
   b.h  c.h
     \  /
      d.c
包含其它头文件
```

**宏定义**

```c
#define PI 3.14159
```

**自定义类型**

```c
struct Circle {
    double x, y, r;
};
```

**类型别名**

```c
typedef struct Circle C;
```

**外部变量声明**

```c
extern double e;
```

**函数声明**

```c
double circleArea(C c);
```

**重定义**

```c
   a.h
  /    \
b.c     c.c
 |       |
b.o     c.o
  \    /
     d
```

一个头文件可能会被多个源文件包含，写在头文件里的函数定义也会因此被预处理器扩展到多个包含该头文件的源文件中，并在编译阶段被编译到等多个不同的目标文件中，这将导致链接错误：multiple definition，多重定义。

**2)去哪里找头文件？**
gcc -I<头文件的附加搜索路径>
**#include <my.h>**
先找-I指定的目录，再找系统目录。
**#include "my.h"**
先找-I指定的目录，再找当前目录，最后找系统目录。
**头文件的系统目录:**
/usr/include - 标准C库
/usr/local/include - 第三方库
/usr/lib/gcc/i686-linux-gnu/5.4.0/include - 编译器库

### 2.6 预处理指令

#include - 将指定的文件内容插至此指令处
#define - 定义宏
#undef - 删除宏
#if - 如果
#ifdef - 如果宏已定义
#ifndef - 如果宏未定义
#else - 否则，与#if/#ifdef/#ifndef配合使用
#elif - 否则如果，与#if/#ifdef/#ifndef配合使用
#endif - 结束判定，与#if/#ifdef/#ifndef配合使用
#error - 产生错误，结束预处理
#warning - 产生警告，继续预处理
#line - 指定行号
代码：line.c

```c
#include <stdio.h>
int main(void) {
    int x = 1000;
    printf("%d\n", __LINE__);
#line 100
    printf("%d\n", __LINE__);
    return 0;
}
```

#pragma - 设定编译器的状态或者指示编译器的操作
#pragma GCC dependency 被依赖文件。
#pragma GCC poison 语法禁忌     **如#pragma GCC poison goto,意思为禁用goto。**
#pragma pack(按几字节对齐：1/2/4/8)
#pragma pack() - 按缺省字节数对齐   **默认对齐数为4**

### 2.7 预定义宏

无需自行定义，预处理器会根据事先设定好的规则将这些宏扩展成其对应的值。

```c
__BASE_FILE__: 正在被处理的源文件名
__FILE__: 所在文件名
__LINE__: 所在行的行号
__FUNCTION__: 所在函数的函数名
__func__: 同__FUNCTION__
__DATE__: 处理日期
__TIME__: 处理时间
__INCLUDE_LEVEL__: 包含层数，从0开始
__cplusplus: C++有定义，C无定义    
```

### 2.8 环境变量

在进程向下文中保存的一些数据：键(功能，是什么)=值(内容)。
env
C_INCLUDE_PATH
C语言头文件的附加搜索路径，相当于-I选项。
CPATH
同C_INCLUDE_PATH
CPLUS_INCLUDE_PATH
C++语言头文件的附加搜索路径，相当于-I选项。
LIBRARY_PATH
链接库路径
LD_LIBRARY_PATH
加载库路径
#include "/.../.../xxx.h"   **移植性差**
#include "xxx.h"
gcc -I/.../... ... - 推荐
C_INCLUDE_PATH/CPATH=/.../...:/...  **易冲突**

## 三、库

a.c -> a.out
foo()
bar()
hum()
main()
单一模型：将程序中所有功能全部实现于一个单一的源文件内部。编译时间长，不易于维护和升级，不易于协发。
分离模型：将程序中的不同功能模块划分到不同的源文件中。缩短编译时间，易于维护和升级，易于协作开发。
a.c   -> a.o \
foo()            | -> ...
bar()            |
b.c  -> b.o /
hum()
a.o \
b.o   | -> 库 + 其它模块 -> ...
c.o   | 
...    /

### 3.1 静态库

静态库的本质就是将多个目标文件打包成一个文件。
链接静态库就是将库中被调用的代码复制到调用模块中。
使用静态库的程序通常会占用较大的空间，库中代码一旦修改，所有使用该库的程序必须重新链接。
使用静态库的程序在运行无需依赖库，其执行效率高。
静态库的形式：libxxx.a
构建静态库：
.c -> .o -> .a
ar -r libxxx.a x.o y.o z.o
**使用静态库：**
gcc ... -lxxx -L<库路径>
export LIBRARY_PATH=<库路径>
gcc ... -lxxx

### 3.2 动态(共享)库

动态库和静态库最大的不同就是，链接动态库并不需要将库中被调用的代码复制到调用模块中，相反被嵌入到调用模块中的仅仅是被调用代码在动态库中的相对地址。
如果动态库中的代码同时为多个进程所用，动态库的实例在整个内存空间中仅需一份，因此动态库也叫共享库或共享对象(Shared Object, so)。
使用动态库的模块所占空间较小，即使修改了库中的代码，只要接口保持不变，无需重新链接。
使用动态库的代码在运行时需要依赖库，执行效率略低。
动态库的形式：libxxx.so
构建动态库：
gcc -c -fpic xxx.c -> xxx.o
               |
   **生成位置无关码库内部的函数调用也用相对地址表示**
gcc -shared -o libxxx.so x.o y.o z.o
使用动态库：
gcc ... -lxxx -L<库路径>
export LIBRARY_PATH=<库路径>
gcc ... -lxxx
运行时所调用的动态库必须位于LD_LIBRARY_PATH环境变量所表示的路径中。
gcc缺省链接共享库(**不做说明，用的都是共享库**)，可通过-static选项强制链接静态库。
**tips：ldd查看依赖关系**

### 3.3 动态加载动态库

```c
#include <dlfcn.h> \ 系统提供的针对动态
-ldl               / 库的动态加载函数集
```

void* dlopen(const char* filename, int flag);
成功返回动态库的句柄，失败返回NULL。
filename - 动态库路径，若只给文件名，则根据LD_LIBRARY_PATH环境变量搜索动态库
flag - 加载方式，可取以下值：
RTLD_LAZY - 延迟加载，使用动态中的符号时才加载
RTLD_NOW - 立即加载
该函数所返回的动态库句柄唯一地标识了系统内核所维护的动态库对象，将作为后续函数调用的数。
void* dlsym(void* handle, const char* symbol);
成功返回函数地址，失败返回NULL。
handle - 动态库句柄
symbol - 符号(函数或全局变量)名
该函数所返回的函数指针是void*类型，需要强制类型转换为实际的函数指针类型才能调用。
int dlclose(void* handle);
成功返回0，失败返回非零。
handle - 动态库句柄
char* dlerror(void);
之前若有错误发生则返回错误信息字符串，否则返回NULL。
代码：load.c

```c
#include <stdio.h>
#include <dlfcn.h>
int main(void) {
    // 动态加载动态库libmath.so
    void* handle = dlopen("shared/libmath.so",
        RTLD_NOW);
    if (! handle) {
        fprintf(stderr, "dlopen: %s\n",
            dlerror());
        return -1;
    }
    // 从动态库中获取add函数的入口地址
    int (*add)(int, int) =
        (int (*)(int, int))dlsym(handle, "add");
    if (! add) {
        fprintf(stderr, "dlsym: %s\n",
            dlerror());
        return -1;
    }
    // 从动态库中获取sub函数的入口地址
    int (*sub)(int, int) =
        (int (*)(int, int))dlsym(handle, "sub");
    if (! sub) {
        fprintf(stderr, "dlsym: %s\n",
            dlerror());
        return -1;
    }
    // 从动态库中获取show函数的入口地址
    void (*show)(int, char, int, int) =
        (void (*)(int, char, int, int))dlsym(
            handle, "show");
    if (! show) {
        fprintf(stderr, "dlsym: %s\n",
            dlerror());
        return -1;
    }
    int a = 30, b = 20;
    show(a, '+', b, add(a, b));
    show(a, '-', b, sub(a, b));
    if (dlclose(handle)) {
        fprintf(stderr, "dlclose: %s\n",
            dlerror());
        return -1;
    }
    return 0;
}
```

**tips:全局变量与静态全局变量的区别是静态全局变量只能在本文件中使用，而局部变量的静态与非静态的区别是静态局部变量的生命周期是整个程序。**

## 四、辅助工具

**1)**查看符号表：nm
列出目标文件(.o)、可执行文件、静态库文件(.a)或动态库文件(.so)中的符号
代码：nm.c

```c
#include <stdio.h>
int a = 0; // 全局变量
static int b = 1; // 静态全局变量
void foo(void) {
    int c = 2; // 局部变量
    static int d = 3; // 静态局部变量
    printf("Hello, World!\n");
}
```

**2)**显示二进制模块的反汇编信息：objdump -S
**3)**删除目标文件(.o)、可执行文件、静态库文件(.a)或动态库文件(.so)中的符号表和调试信息：strip
**4)**查看可执行程序文件或动态库文件所依赖的动态库文件：ldd

## 五、错误号和错误信息

**1)**通过函数的返回值表达错误
返回整数的函数：通过返回合法值域以外的值表示错误
int age(char const* name) {
    ...
    return 1000;
}
返回指针的函数：通过返回NULL指针表示错误
不需要通过返回值输出信息的函数：返回0表示成功，返回-1表示失败。
int delete(char const* filename) {
    ...
    return 0;
    ...
    return -1;
}
**2)**通过错误号和错误信息表示产生错误的具体原因
#include <errno.h>
全局变量：errno，整数，标识最近一次系统调用的错误
#include <string.h>
char* strerror(int errnum); // 根据错误号返回错误信息
#include <stdio.h>
void perror(const char* s); // 打印最近错误的错误信息(打印在标准出错上)
printf函数的%m标记被替换为最近错误的错误信息
代码：errno.c

```c
#include <stdio.h>
#include <string.h>
#include <errno.h>
int main(void) {
    FILE* fp = fopen("none", "r");
    if (!fp) {
        printf("fopen: %d\n", errno);
        printf("fopen: %s\n", strerror(errno));
        perror("fopen");
        printf("fopen: %m\n");
        return -1;
    }
    // ...
    fclose(fp);
    return 0;
}
```

虽然所有的错误号都不是0，但是因为在函数执行成功的情况下错误号全局变量errno不会被清0，因此不能用errno是否为0作为函数成功失败的判断条件，是否出错还是应该根据函数的返回值来决定。
返回值 = 函数调用(...);
if (返回值表示函数调用失败) {
    根据errno判断发生了什么错误
    针对不同的错误提供不同的处理
}
代码：iferr.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
int main(void) {
    FILE* fp = fopen("none", "r");
    if (!fp) {
        perror("fopen");
        return -1;
    }
    int* pi = malloc(sizeof(int));
//  if (errno) {
    if (!pi) {
        perror("malloc");
        return -1;
    }
    // ...
    free(pi);
    fclose(fp);
    return 0;
}
```

## 六、环境变量

每个进程都有一张独立的环境变量表，其中的每个条目都是一个形如“键=值”形式的环境变量。
env
全局变量：environ，需要自己在代码做外部声明。
environ->|    *    |->AAA=aaa\0
                  |    *    |->BBB=bbb\0
                  |    *    |->CCC=ccc\0
                  |NULL|
所谓环境变量表就是一个以NULL指针结束的字符指针数组，其中的每个元素都是一个字符指针，指向一个以空字符结尾的字符串，该字符串就是形如”键=值”形式的环境变量。
argv-> *      *      *     NULL
             |       |      |
          a.out  -c    b.c
根据环境变量名获取其值
char* getenv(char const* name);
成功返回变量名匹配的变量值，失败返回NULL。
name - 环境变量名，即等号左边的部分
添加或修改环境变量
int putenv(char* string);
成功返回0，失败返回-1。
string - 形如“键=值”形式的环境变量字符串
若其键已存在，则修改其中，若其键不存在，则添加新变量
添加或修改环境变量
int setenv(const char* name, const char* value,
    int overwrite);
成功返回0，失败返回-1。
name - 环境变量名，即等号左边的部分
value - 环境变量值，即等号右边的部分
overwrite - 当name参数所表示的环境变量名已存在，此参数取0则保持该变量的原值不变，若此参数取非0，则将该变量的值修改为value。
删除环境变量
int unsetenv(const char* name);
成功返回0，失败返回-1。
name - 环境变量名，即等号左边的部分
清空环境变量
int clearenv(void);
成功返回0，失败返回-1。
代码：env.c

```c
#include <stdio.h>
#include <stdlib.h>
void penv(char** env) {
    printf("---- 环境变量 ----\n");
    while (env && *env)
        printf("%s\n", *env++);
    printf("------------------\n");
}
int main(int argc, char* argv[], char* envp[]) {
    extern char** environ;
    printf("%p %p\n", environ, envp);
    penv(environ/*envp*/);
    printf("HOME=%s\n", getenv("HOME"));
    /*
    if (putenv("NAME=minwei") == -1) {
        perror("putenv");
        return -1;
    }
    */
    if (setenv("NAME", "minwei", 0) == -1) {
        perror("setenv");
        return -1;
    }
    penv(environ);
    /*
    if (putenv("NAME=youchengwei") == -1) {
        perror("putenv");
        return -1;
    }
    */
//  if (setenv("NAME", "youchentwei", 0) == -1) {
    if (setenv("NAME", "youchentwei", 1) == -1) {
        perror("setenv");
        return -1;
    }
    penv(environ);
    if (unsetenv("NAME") == -1) {
        perror("unsetenv");
        return -1;
    }
    penv(environ);
    if (clearenv() == -1) {
        perror("clearenv");
        return -1;
    }
    printf("%p\n", environ);
    penv(environ);
    return 0;
}
```

## 七、内存

**1)**虚拟内存、物理内存、半导体内存和换页文件
虚拟内存：地址空间，虚拟的存储区域，应用程序所访问的都是虚拟内存。
物理内存：存储空间，实际的存储区域，只有系统内核可以访问物理内存。
虚拟内存和物理内存之间存在对应关系，当应用程序访问虚拟内存时，系统内核会依据这种对应关系找到与之相应的物理内存。上述对应关系存储在内核中的内存映射表中。
物理内存包括半导体内存和换页文件两部分。
当半导体内存不够用时，可以把一些长期闲置的代码和数据从半导体内存中缓存到换页文件中，这叫页面换出，一旦需要使用被换出的代码和数据，再把它们从换页文件恢复到半导体内存中，这叫页面换入。因此，系统中的虚拟内存比半导体内存大得多。
**2)**进程映射(Process Maps)
每个进程都拥有独立的4G字节的虚拟内存，分别被映射到不同的物理内存区域。
内存映射和换入换出都是以页为单位，1页=4096字节。
4G虚拟内存中高地址的1G被映射到内核的代码和数据区，这1个G在各个进程间共享。用户的应用程序只能直接访问低地址的3个G虚拟内存，因此该区域被称为用户空间，而高地址的1个G虚拟内存则被称为内核空间。用户空间中的代码只能直接访问用户空间的数据，如果要想访问内核空间中的代码和数据必须借助专门的系统调用完成。
用户空间的3G虚拟内存可以进一步被划分为如下区域：

```c
          ------------------
             系统内核(1G)
高地址     ------------------
             命令行参数  (如argc、argv等)
             和环境变量
          ------------------
            栈区：非静态局部变量
          -  -  -  -  -  -  -  -
                   v
  3G
                   ^
          -  -  -  -  -  -  -  -
            堆区：动态内存分配(malloc函数族)
          -----------------
            BSS区：无初值的全局和静态局部变量
          -----------------
            数据区：非const型有初值的全局和静态局部变量
          -----------------
            只读常量：字面值常量，const型有初值的全局和静态局部变量
            代码区(正文段)：可执行指令
低地址     -----------------
```

**tips:**const一般修饰可改可不改，static一般修饰放置的位置，const变量必须要初始化。堆和栈其实是一个东西(先进后出)，人为的把它们分开，一个是自动的(栈)，一个是用函数分配的(堆)。
代码：maps.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
const int const_global = 10; // 常全局变量
int init_global = 10; // 初始化全局变量
int uninit_global; // 未初始化全局变量
int main(int argc, char* argv[]) {
    // 常静态变量
    const static int const_static = 10;
    // 初始化静态变量
    static int init_static = 10;
    // 未初始化静态变量
    static int uninit_static;
    // 常局部变量
    const int const_local = 10;
    int prev_local; // 前局部变量
    int next_local; // 后局部变量
    // 前堆变量
    int* prev_heap = malloc(sizeof(int));
    // 后堆变量
    int* next_heap = malloc(sizeof(int));
    // 字面值常量
    char const* literal = "literal";
    extern char** environ; // 环境变量
    printf("---- 命令行参数与环境变量 ----\n");
    printf("         环境变量：%p\n", environ);
    printf("       命令行参数：%p\n", argv);
    printf("-------------- 栈 ------------\n");
    printf("       后局部变量：%p\n",
        &next_local);
    printf("       前局部变量：%p\n",
        &prev_local);
    printf("       常局部变量：%p\n",
        &const_local);
    printf("-------------- 堆 ------------\n");
    printf("         后堆变量：%p\n", next_heap);
    printf("         前堆变量：%p\n", prev_heap);
    printf("------------- BSS ------------\n");
    printf(" 未初始化全局变量：%p\n",
        &uninit_global);
    printf(" 未初始化静态变量：%p\n",
        &uninit_static);
    printf("------------ 数据 ------------\n");
    printf("   初始化静态变量：%p\n",
        &init_static);
    printf("   初始化全局变量：%p\n",
        &init_global);
    printf("------------ 代码 ------------\n");
    printf("       常静态变量：%p\n",
        &const_static);
    printf("       字面值常量：%p\n", literal);
    printf("       常全局变量：%p\n",
        &const_global);
    printf("             函数：%p\n",
        main);
    printf("------------------------------\n");
    return 0;
}
```

通过size命令查看一个可执行程序的代码区、数据区和BSS区的大小。
每个进程的用户空间都拥有独立的从虚拟内存到物理内存的映射，谓之进程间的内存壁垒。
代码：vm.c

```c
#include <stdio.h>
int g_vm = 0;
int main(void) {
    printf("虚拟内存地址：%p\n", &g_vm);
    printf("输入一个整数：");
    scanf("%d%*c", &g_vm);
    printf("启动另一个进程，输入不同数据，"
        "按<回车>键继续...");
    getchar();
    printf("虚拟内存数据：%d\n", g_vm);
    return 0;
}
```

**3)**内存的分配与释放

```c
      malloc/calloc/realloc/free
                   |
                   v
                brk/sbrk
                   |
                   v
               mmap/munmap
                   |
                   v
               kmalloc/kfree
```

以增加方式分配或释放虚拟内存

```c
分配：   映射         +        占有
         |                     |
 在地址空间(虚拟内存)和        指定内存空
 存储空间(物理内存)之间        间的归属性
       建立映射关系               
释放： 放弃占有       +        解除映射
         |                      |
     解除对内存空       消除地址空间(虚拟内存)和存储
     间的归属约束       空间(物理内存)之间的映射关系
```

#include <unistd.h>
void* sbrk(intptr_t increment);

```c
堆顶->-  -  -  -  -  -  -  -
                    堆区
           -----------------
sbrk(10)
堆顶->-  -  -  -  -  -  -  -
                  10字节
           -  -  -  -  -  -  -  -<-返回值
                    堆区
           -----------------
sbrk(-10)
           -  -  -  -  -  -  -  -<-返回值
                  10字节
堆顶->-  -  -  -  -  -  -  -
                    堆区
           -----------------
```

成功返回调用该函数之前的堆顶指针，失败返回-1。

increment
```c
>0 - 堆顶指针上移，增大堆空间，分配虚拟内存
<0 - 堆顶指针下移，缩小堆空间，释放虚拟内存
=0 - 不分配也不释放虚拟内存，仅仅返回当前堆顶指针
```

系统内核维护一个指针，指向堆内存的顶端，即有效堆内存中最后一个字节的下一个位置。sbrk函数根据增量参数increment调整该指针的位置，同时返回该指针原来的位置，期间若发生内存耗尽或空闲，则自动追加或取消相应内存页的映射。
代码：sbrk.c

```c
#include <stdio.h>
#include <unistd.h>
int main(void) {
    setbuf(stdout, NULL);
    int* p1 = (int*)sbrk(sizeof(int));
    if (p1 == (int*)-1) {
        perror("sbrk");
        return -1;
    }
    *p1 = 0;
    printf("%d\n", *p1);
    double* p2 = (double*)sbrk(sizeof(double));
    if (p2 == (double*)-1) {
        perror("sbrk");
        return -1;
    }
    *p2 = 1.2;
    printf("%g\n", *p2);
    char* p3 = (char*)sbrk(256 * sizeof(char));
    if (p3 == (char*)-1) {
        perror("sbrk");
        return -1;
    }
    sprintf(p3, "Hello, World!");
    printf("%s\n", p3);
    /*
    if (sbrk(-(256 * sizeof(char) +
        sizeof(double) +
        sizeof(int))) == (void*)-1) {
        perror("sbrk");
        return -1;
    }
    */
    if (brk(p1) == -1) {
        perror("brk");
        return -1;
    }
    return 0;
}
```

以绝对地址的方式分配或释放虚拟内存
int brk(void* end_data_segment);
成功返回0，失败返回-1。
end_data_segment

```c
>当前堆顶，分配虚拟内存
<当前堆顶，释放虚拟内存
=当前堆顶，空操作
```

```c
堆顶->-  -  -  -  -  -  -  -<-void* p = sbrk(0);
                    堆区
           -----------------
brk(p+10)
           -  -  -  -  -  -  -  -<-p+10
                  10字节
堆顶->-  -  -  -  -  -  -  -
                    堆区
           -----------------
brk(p)
堆顶->-  -  -  -  -  -  -  -<-p
                    堆区
           -----------------
```

系统内核维护一个指针，指向当前堆顶，brk函数根据指针参数end_data_segment设置堆顶的新位置，期间若发生内存耗尽或空闲，则自动追加或取消相应内存页的映射。
代码：brk.c

```c
#include <stdio.h>
#include <unistd.h>
/*
 * xxxIIIIDDDDDDDDCC...C
 *    ^   ^       ^     ^
 *    p1  p2      p3
 */
int main(void) {
    setbuf(stdout, NULL);
    int* p1 = (int*)sbrk(0);
    if (p1 == (int*)-1) {
        perror("sbrk");
        return -1;
    }
    double* p2 = (double*)(p1 + 1);
    if (brk(p2) == -1) {
        perror("brk");
        return -1;
    }
    *p1 = 0;
    printf("%d\n", *p1);
    char* p3 = (char*)(p2 + 1);
    if (brk(p3) == -1) {
        perror("brk");
        return -1;
    }
    *p2 = 1.2;
    printf("%g\n", *p2);
    void* p4 = p3 + 256;
    if (brk(p4) == -1) {
        perror("brk");
        return -1;
    }
    sprintf(p3, "Hello, World!");
    printf("%s\n", p3);
    if (brk(p1) == -1) {
        perror("brk");
        return -1;
    }
    return 0;
}
```

**tips：**一般分配内存用sbrk，释放内存用brk。printf等的缓冲区在堆内存里，在brk.c程序中，应该把标准输出给关掉(setbuf(stdout,Null)),因为我们操作的内存在堆内存，而printf等的缓冲也在堆内存中，这样可能会相互影响。

**4)**建立虚拟内存到物理内存或文件的映射

#include <sys/mman.h>
void* mmap(void* start, size_t length, int prot,
    int flags, int fd, off_t offset);
成功返回映射区虚拟内存的起始地址，失败返回MAP_FAILED(void类型的-1)。
start - 映射区虚拟内存的起始地址，NULL表示自动选择
length - 映射区的字节数，自动按页取整
prot - 访问权限，可取以下值：
PROT_READ - 可读
PROT_WRITE - 可写
PROT_EXEC - 可执行
PROT_NONE - 不可访问
flags - 映射标志，可取以下值：
MAP_ANONYMOUS - 匿名映射(往文件里映射就是有名映射)，将虚拟内存映射到物理内存，函数的最后两个参数fd和offset被忽略
MAP_PRIVATE - 私有映射，将虚拟内存映射到文件的内存缓冲区中而非磁盘文件
MAP_SHARED - 共享映射，将虚拟内存映射到磁盘文件中
MAP_DENYWRITE - 拒写映射，文件中被映射区域不能存在其它写入操作
MAP_FIXED - 固定映射，若在start上无法创建映射，则失败(无此标志系统会自动调整)
MAP_LOCKED - 锁定映射，禁止被换出到换页文件
fd - 文件描述符
offset - 文件偏移量，自动按页对齐
解除虚拟内存到物理内存或文件的映射
int munmap(void start, size_t length);
成功返回0，失败返回-1。
start - 映射区的起始地址
length - 映射区的字节数
代码：mmap.c

```c
#include <stdio.h>
#include <unistd.h>
#include <sys/mman.h>
int main(void) {
    char* psz = mmap(NULL, 8192,
        PROT_READ | PROT_WRITE,
        MAP_ANONYMOUS | MAP_PRIVATE, 0, 0);
    if (psz == MAP_FAILED) {
        perror("mmap");
        return -1;
    }
    sprintf(psz, "第一页");
    sprintf(psz + 4096, "第二页");
    printf("%s\n", psz);
    printf("%s\n", psz + 4096);
    if (munmap(psz, 4096) == -1) {
        perror("munmap");
        return -1;
    }
    //printf("%s\n", psz);
    printf("%s\n", psz + 4096);
    if (munmap(psz + 4096, 4096) == -1) {
        perror("munmap");
        return -1;
    }
    return 0;
}
```

### 八、系统调用

```c
           应用程序--------------+
  vi/emacs/gftp/firefox         |
              |                 |
       标准库、第三方库            |
        C/C++/Qt/X11            |
              |                 |
            系统调用<------------+
     brk/sbrk/mmap/munmap
```

1.Linux系统内核提供了一套用于实现各种系统功能的子程序，谓之系统调用。程序编写者可以象调用普通C语言函数一样调用这些系统调用函数，以访问系统内核提供的各种服务。
2.系统调用函数在形式上与普通C语言函数并无差别。二者的不同之处在于，前者工作在内核态，而后者工作在用户态。
3.在Intel的CPU上运行代码分为四个安全级别：Ring0、Ring1、Ring2和Ring3。Linux系统只使用了Ring0和Ring3。用户代码工作在Ring3级，而内核代码工作在Ring0级。一般而言用户代码无法访问Ring0级的资源，除非借助系统调用，使用户代码得以进入Ring0级，使用系统内核提供的功能。
4.系统内核内部维护一张全局表sys_call_table，表中的每个条目记录着每个系统调用在内核代码中的实现入口地址。
5.当用户代码调用某个系统调用函数时，该函数会先将参数压入堆栈，将系统调用标识存入eax寄存器，然后通过int 80h指令触发80h中断。
6.这时程序便从用户态(Ring3)进入内核态(Ring0)。
7.工作系统内核中的中断处理函数被调用，80h中断的处理函数名为system_call，该函数先从堆栈中取出参数，再从eax寄存器中取出系统调用标识，然后再从sys_call_table表中找到与该系统调用标识相对应的实现代码入口地址，挈其参数调用该实现，并将处理结果逐层返回到用户代码中。

## 九、文件

### 9.1 文件系统的物理结构

**1)**硬盘的物理结构：驱动臂、盘片、主轴、磁头、控制器
**2)**磁表面存储器的读写原理
硬盘片的表面覆盖着薄薄的磁性涂层，涂层中含有无数微小的磁性颗粒，谓之磁畴。相邻的若干磁畴组成一个磁性存储元，以其剩磁的极性表示二进制数字0和1。为磁头的写线圈中施加脉冲电流，可把一位二进制数组转换为磁性存储元的剩磁极性。利用磁电变换，通过磁头的读线圈，可将磁性存储元的剩磁极性转换为相应的电信号，表示二进制数。
**3)**磁道和扇区
磁盘旋转，磁头固定，每个磁头都会在盘片表面画出一个圆形轨迹。改变磁头位置，可以形成若干大小不等的同心圆，这些同心圆就叫做磁道(Track)。每张盘片的每个表面上都有成千上万个磁道。一个磁道，以512字节为单位，分成若干个区域，其中的每个区域就叫做一个扇区(Sector)。扇区是文件存储的基本单位。
**4)**柱面、柱面组、分区和磁盘驱动器
硬盘中，不同盘片相同半径的磁道所组成的圆柱称为柱面(Cylinder)。整个硬盘的柱面数与每张盘片的磁道数相等。
硬盘上的每个字节需要通过以下参数定位：
磁头号：确定哪个盘面    \
柱面号：确定哪个磁道     > 磁盘I/O
扇区号：确定哪个区域     /
偏移量：确定扇区内的位置
若干个连续的柱面构成一个柱面组
若干个连续的柱面组构成一个分区
每个分区都建有独立的文件系统
若干分区构成一个磁盘驱动器

### 9.2 文件系统的逻辑结构

```c磁盘驱动器：| 分区 | 分区 | 分区 |
分区：| 引导块 | 超级块 | 柱面组 | 柱面组 | 柱面组 |
柱面组：
| 引导块副本| 柱面组信息| i节点映 | 块位图 | i节点表 | 数据块集|
i节点号：431479
i节点
    文件元数据
    100 | 200 | 300
根据目录文件中记录的i节点编号检索i节点映射表，获得i节点下标，用该下标查i节点表，获得i节点，i节点中包含了数据块索引表，利用数据块索引从数据块集中读取数据块，即获得文件数据。
直接块：存储文件实际数据内容
间接块：存储下级文件数据块索引表
100
-----
xxx

200
-----
xxx

300
----
400 | 500 | 600
```

### 9.3 文件分类

普通文件(-)：可执行程序、文本、图片、音频、视频、网页
目录文件(d)：该目录中每个硬链接名和i节点号的对应表
符号链接文件(l)：存放目标文件的路径
管道文件(p)：有名管道，进程间通信
套接字文件(s)：进程间通信
块设备文件(b)：按块寻址，顺序或随机读写
字符设备文件(c)：按字节寻址，只能以字节为单位顺序读写

### 9.4 文件的打开与关闭

打开：在系统内核中建立一套数据结构，用于访问文件

```c
进程表项
    ...
    文件描述符表
        |文件描述符标志 | 文件表项指针 | 0   标准输入键盘用
        |文件描述符标志 | 文件表项指针 | 1   标准输出显示器用
        |文件描述符标志 | 文件表项指针 | 2   标准出错用
        ...                   |      ^
 +----------------------------+      |
 |                                文件描述符
 v
文件表项
    文件状态标志
    文件读写位置
    v节点指针
    ...|
 +-----+
 |
 v
v节点
    i节点内容(如某个文件)
    ...
```

关闭：释放打开文件过程中建立的数据结构

FILE* fp = fopen("reame.txt", "r");
fread(fp, ...);
#include <fcntl.h>
打开已有的文件或创建新文件
int open(const char* pathname, int flags,
    mode_t mode);
成功返回文件描述符(一定是大于等于0的int)，失败返回-1。

pathname - 文件路径
flags - 状态标志，可取以下值：
O_RDONLY - 只读  \
O_WRONLY - 只写  > 只选其一，不能位或
O_RDWR - 读写      /
O_APPEND - 追加
O_CREAT - 创建，不存在即创建，已存在即打开，除非与以下两个标志(O_EXCL,O_TRUNC)之一合用，它的情况会有所不同，有O_CREAT标志mode参数才有效。
O_EXCL - 排它，已存在即失败
O_TRUNC - 清空，已存在即清空，同时有O_WRONLY或O_RDWR

**tips:**O_CREAT ,O_EXCL ,O_TRUNC得配合使用，不能单独使用

O_SYNC - 写同步，在数据被写到磁盘之前写操作不会完成，读操作本来就是同步的，此标志对读操作没有意义
O_ASYNC - 异步，在文件可读写时产生一个SIGIO信号，在对信号的处理过程中读写I/O就绪的文件，只能用于终端设备或网络套接字，而不能用于磁盘文件
O_NONBLOCK - 非阻塞，读操作不会因为无数据可读而阻塞，写操作也不会因为缓冲区满而阻塞，相反会返回失败，并设置特定的errno

**tips：**O_SYNC，O_ASYNC ，O_NONBLOCK这三个用的不多

mode - 权限模式，三位八进制：0XXX
                                XXX依次为拥有者用户 同组用户 其它用户
4: 可读
2: 可写
1: 可执行
所创建文件的实际权限除了跟mode参数有关，还受权限掩码的影响。
mode=0666
umask=0002
权限=mode&~umask=0664

创建新文件
int creat(const char* pathname, mode_t mode);
flags: O_WRONLY | O_CREAT | O_TRUNC (默认)
打开已有文件
int open(const char* pathname, int flags);
关闭文件
int close(int fd);
成功返回0，失败返回-1。
fd - 文件描述符
代码：open.c  

```c
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
int main(void) {
    int fd1 = open("open.txt", O_RDWR | O_CREAT |
        O_TRUNC, 0666);
    if (fd1 == -1) {
        perror("open");
        return -1;
    }
    printf("fd1 = %d\n", fd1);
    int fd2 = open("open.txt", O_RDONLY);
    if (fd2 == -1) {
        perror("open");
        return -1;
    }
    printf("fd2 = %d\n", fd2);
    close(fd2);
    close(fd1);
    return 0;
}
```

作为文件描述符表项在文件描述符表中的下标，合法的文件描述符一定是大于或等于0的整数。每次产生新的文件描述符表项，系统总是从下标0开始在文件描述符表中寻找最小的未使用项。每关闭一个文件描述符，无论被其索引的文件表项和v节点是否被删除，与之对应的文件描述符表项一定会被标记为未使用，并在后续操作中为新的文件描述符所占用。系统内核缺省为每个进程打开三个文件描述符：

```c
#include <unistd.h>
#define STDIN_FILENO    0 // 标准输入，即键盘
#define STDOUT_FILENO 1 // 标准输出，终端屏幕，有缓冲
#define STDERR_FILENO  2 // 标准错误，终端屏幕，无缓冲
        UC     C        C++
标准输入  0    stdin     cin
标准输出  1    stdout    cout
标准错误  2    stderr    cerr
数据类型 int   FILE*     iostream
文件描述符是用户程序和系统内核关于文件的唯一联系方式。
```

**tips：**文件描述符可以进行I-O重定向，如0<i.txt,把0给关了，紧接着打开i.txt,那么i.txt的文件描述符就是0，scanf读的是文件描述符为0的文件。

### 9.5 文件的读取和写入

向指定文件写入字节流
ssize_t write(int fd, const void* buf, size_t count);
成功返回实际写入的字节数(0表示未写入)，失败返回-1。
fd - 文件描述符
buf - 内存缓冲区
count - 期望写入的字节数
ssize_t read(int fd, void* buf, size_t count); **tips:**ssize_t为有符号的整形
成功返回实际读取的字节数(0表示读到文件尾)，失败返回-1。
fd - 文件描述符
buf - 内存缓冲区
count - 期望读取的字节数
代码：write.c、read.c

```c
write.c

#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
int main(void) {
    int fd = open("write.txt", O_WRONLY |
        O_CREAT | O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    const char* text = "Hello, World!";
    printf("写入内容：%s\n", text);
    size_t towrite = strlen(text) * sizeof(
        text[0]);
    ssize_t written = write(fd, text,
        towrite);
    if (written == -1) {
        perror("write");
        return -1;
    }
    printf("期望写入%u字节，实际写入%d字节。\n",
        towrite, written);
    close(fd);
    return 0;
}

read.c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
int main(void) {
    int fd = open("read.txt", O_RDONLY);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    char text[256];
    size_t toread = sizeof(text) - sizeof(
        text[0]);
    ssize_t readed = read(fd, text, toread);
    if (readed == -1) {
        perror("read");
        return -1;
    }
    printf("期望读取%u字节，实际读取%d字节。\n",
        toread, readed);
    text[readed / sizeof(text[0])] = '\0';
    printf("读取内容：%s\n", text);
    close(fd);
    return 0;
}
```

基于系统调用的文件读写本来就是面向二进制字节流的，因此对二进制读写而言，无需做任何额外的工作。如果要求文件中内容必须是可阅读的，那么就必须通过格式化和文本解析处理二进制形式的数据和文本字符串之间的转换。
代码：binary.c、text.c

```c
binary.c

#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
int main(void) {
    int fd = open("binary.dat", O_WRONLY |
        O_CREAT | O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    char name[256] = "张飞";
    if (write(fd, name, sizeof(name)) == -1) {
        perror("write");
        return -1;
    }
    unsigned int age = 38;
    if (write(fd, &age, sizeof(age)) == -1) {
        perror("write");
        return -1;
    }
    double salary = 20000;
    if (write(fd, &salary,
        sizeof(salary)) == -1) {
        perror("write");
        return -1;
    }
    struct Employee {
        char name[256];
        unsigned int age;
        double salary;
    }   employee = {"赵云", 25, 10000};
    if (write(fd, &employee, sizeof(
        employee)) == -1) {
        perror("write");
        return -1;
    }
    close(fd);
    if ((fd = open("binary.dat",
        O_RDONLY)) == -1) {
        perror("open");
        return -1;
    }
    if (read(fd, name, sizeof(name)) == -1) {
        perror("read");
        return -1;
    }
    printf("姓名：%s\n", name);
    if (read(fd, &age, sizeof(age)) == -1) {
        perror("read");
        return -1;
    }
    printf("年龄：%u\n", age);
    if (read(fd, &salary, sizeof(
        salary)) == -1) {
        perror("read");
        return -1;
    }
    printf("工资：%g\n", salary);
    if (read(fd, &employee, sizeof(
        employee)) == -1) {
        perror("read");
        return -1;
    }
    printf("员工：%s, %u, %g\n", employee.name,
        employee.age, employee.salary);
    close(fd);
    return 0;
}

text.c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
int main(void) {
    int fd = open("text.txt", O_WRONLY |
        O_CREAT | O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    char name[256] = "张飞";
    unsigned int age = 38;
    double salary = 20000;
    char buf[1024];
    sprintf(buf, "%s %u %.2lf\n", name, age,
        salary);
    if (write(fd, buf, strlen(buf) * sizeof(
        buf[0])) == -1) {
        perror("write");
        return -1;
    }
    struct Employee {
        char name[256];
        unsigned int age;
        double salary;
    }   employee = {"赵云", 25, 10000};
    sprintf(buf, "%s %u %.2lf\n", employee.name,
        employee.age, employee.salary);
    if (write(fd, buf, strlen(buf) * sizeof(
        buf[0])) == -1) {
        perror("write");
        return -1;
    }
    close(fd);
    if ((fd = open("text.txt",
        O_RDONLY)) == -1) {
        perror("open");
        return -1;
    }
    memset(buf, 0, sizeof(buf));
    if (read(fd, buf, sizeof(buf) - sizeof(
        buf[0])) == -1) {
        perror("read");
        return -1;
    }
    sscanf(buf, "%s%u%lf%s%u%lf", name, &age,
        &salary, employee.name, &employee.age,
        &employee.salary);
    printf("姓名：%s\n", name);
    printf("年龄：%u\n", age);
    printf("工资: %g\n", salary);
    printf("员工：%s, %u, %g\n", employee.name,
        employee.age, employee.salary);
    close(fd);
    return 0;
}
```

### 9.6 顺序与随机读写

每个打开的文件都有一个与其相关的文件读写位置保存在文件表项中，用以记录从文件头开始计算的字节偏移。文件读写位置通常是一个非负的整数，用off_t类型表示，在32位系统上被定义为long int，而在64位系统上则被定义为long long int。打开一个文件时，除非指定了O_APPEND标志，否则文件读写位置一律被设为0，即文件首字节的位置。每一次读写操作都从当前的文件读写位置开始，并根据所读写的字节数，同步增加文件读写位置，为下一次读写做好准备。因为文件读写位置是保存在文件表项而不是v节点中的，因此通过多次打开同一个文件得到多个文件描述符，各自拥有各自的文件读写位置。
人为调整文件读写位置
off_t lseek(int fd, off_t offset, int whence);
成功返回调整后的文件读写位置，失败返回-1。
fd - 文件描述符
offset - 文件读写位置相对于whence参数的偏移量
whence
SEEK_SET - 从文件开始
SEEK_CUR - 从当前位置开始
SEEK_END - 从文件尾开始
lseek函数仅仅是修改文件表项中的文件读写位置，并不引发实际的I/O操作，速度很快。
lseek(fd, 10, SEEK_SET);//从文件头开始偏移10个字节
lseek(fd, -10, SEEK_END);//从文件尾往前算10个字节
lseek(fd, 0, SEEK_CUR); // 返回当前读写位置
lseek(fd, 0, SEEK_END); // 返回文件总字节数
lseek(fd, -10, SEEK_SET); // 错误
lseek(fd, 10, SEEK_END); // 允许，空洞部分补0
代码：seek.c

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
int main(void) {
    int fd = open("seek.txt", O_RDWR | O_CREAT |
        O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    const char* text = "Hello, World!";
    if (write(fd, text, strlen(text) * sizeof(
        text[0])) == -1) {
        perror("write");
        return -1;
    }
    if (lseek(fd, -6, SEEK_CUR) == -1) {
        perror("lseek");
        return -1;
    }
    off_t pos = lseek(fd, 0, SEEK_CUR);
    if (pos == -1) {
        perror("lseek");
        return -1;
    }
    printf("当前读写位置：%ld\n", pos); // 7
    text = "Linux";
    if (write(fd, text, strlen(text) * sizeof(
        text[0])) == -1) {
        perror("write");
        return -1;
    }
    // 文件的读写位置可以在文件尾之后
    if (lseek(fd, 8, SEEK_END) == -1) {
        perror("lseek");
        return -1;
    }
    text = "<-这里有个洞洞！";
    if (write(fd, text, strlen(text) * sizeof(
        text[0])) == -1) {
        perror("write");
        return -1;
    }
    off_t size = lseek(fd, 0, SEEK_END);
    if (size == -1) {
        perror("lseek");
        return -1;
    }
    printf("文件大小：%ld\n", size);
    // 文件的读写位置不能在文件头之前
//  if (lseek(fd, -8, SEEK_SET) == -1) {
    if (lseek(fd, -size-1, SEEK_END) == -1) {
        perror("lseek");
        return -1;
    }
    close(fd);
    return 0;
}
```

### 9.7 标准I/O和系统I/O

```c
             应用程序----------+
                |             |
                v             |
           标准(库)I/O         |
        fopen/fwrite/fclose   |  
                |             |
                v             |
            系统(库)I/O        |
       open/write/close <-----+
```

代码：stdio.c、sysio.c

```c
stdio.c

#include <stdio.h>
int main(void) {
    FILE* fp = fopen("sysio.dat", "wb");
    if (!fp) {
        perror("fopen");
        return -1;
    }
    //setbuf(fp, NULL);
    for (unsigned int i = 0; i < 1000000; ++i)
        fwrite(&i, sizeof(i), 1, fp);
    fclose(fp);
    return 0;
}

sysio.c
   
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
int main(void) {
    int fd = open("sysio.dat", O_WRONLY |
        O_CREAT | O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    for (unsigned int i = 0; i < 1000000; ++i)
        write(fd, &i, sizeof(i));
    close(fd);
    return 0;
}
```

标准库通过缓冲区优化，减少系统调用的次数，降低在用户态和内核态之间来回切换的频率，提高运行速度，缩短运行时间。

### 9.8  复制文件描述符(表项)

```c
进程表项
    ...
    文件描述符表
        |文件描述符标志 | 文件表项指针 | 0
        |文件描述符标志 | 文件表项指针 | 1
        |文件描述符标志 | 文件表项指针 | 2
int fd  = open(...); // fd: 3
进程表项
    ...
    文件描述符表
        |文件描述符标志 | 文件表项指针 | 0
        |文件描述符标志 | 文件表项指针 | 1
        |文件描述符标志 | 文件表项指针 | 2
        |文件描述符标志 | 文件表项指针 | 3 -> 文件表项
int dup(int oldfd);
成功返回目标文件描述符，失败返回-1。
oldfd - 源文件描述符
int fd2 = dup(fd); // fd2: 7
进程表项
    ...
    文件描述符表
        |文件描述符标志 | 文件表项指针 | 0
        |文件描述符标志 | 文件表项指针 | 1
        |文件描述符标志 | 文件表项指针 | 2
        |文件描述符标志 | 文件表项指针 | 3->文件表项->v节点
        ...                                 ^                                                                   ^
        |文件描述符标志 | 文件表项指针 | 7 ------+
fd2(7)和fd(3)对应同一个文件表项，访问同一个文件。
dup函数将oldfd参数所对应的文件描述符表项复制到文件描述符表第一个空闲项中，同时返回该表项所对应的文件描述符。
close(fd);
close(fd2);
int dup2(int oldfd, int newfd);
成功返回目标文件描述符，失败返回-1。
oldfd - 源文件描述符
newfd - 目标文件描述符
dup2函数在复制oldfd参数所标识的源文件描述符表项时，会首先检查由newfd参数所标识的目标文件描述符表项是否空闲，若空闲则直接将前者复制给后者，否则会先将目标文件描述符newfd关闭，再行复制。
fd1 = open("1.txt", ...); --> 文件表项 \
                                         v节点
fd2 = open("1.txt", ...); --> 文件表项 /

fd1 = open("2.txt", ...); \
                              > 文件表项 -> v节点
fd2 = dup(fd1);           / 
```

代码：dup.c

```c
#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <fcntl.h>
int main(void) {
    int fd1 = open("dup.txt", O_RDWR | O_CREAT |
        O_TRUNC, 0644);
    if (fd1 == -1) {
        perror("open");
        return -1;
    }
    printf("fd1 = %d\n", fd1);
    int fd2 = dup(fd1);
    if (fd2 == -1) {
        perror("dup");
        return -1;
    }
    printf("fd2 = %d\n", fd2);
    int fd3 = dup2(fd2, 100);
    if (fd3 == -1) {
        perror("dup2");
        return -1;
    }
    printf("fd3 = %d\n", fd3);
    const char* text = "Hello, World!";
    if (write(fd1, text, strlen(text) * sizeof(
        text[0])) == -1) {
        perror("write");
        return -1;
    }
    if (lseek(fd2, -6, SEEK_CUR) == -1) {
        perror("lseek");
        return -1;
    }
    text = "Linux";
    if (write(fd3, text, strlen(text) * sizeof(
        text[0])) == -1) {
        perror("write");
        return -1;
    }
    close(fd3);
    close(fd2);
    close(fd1);
    return 0;
}
```

### 9.9 文件控制

int fcntl(int fd, int cmd, ...);
**复制文件描述符(表项)**
int fcntl(int oldfd, F_DUPFD, int newfd);
成功返回目标文件描述符，失败返回-1。
oldfd - 源文件描述符
newfd - 目标文件描述符
该函数类似dup2函数，但略有不同。如果newfd处于打开状态，该函数并不会象dup2函数那样关闭它，而是另外寻找一个比它大的最小的空闲文件描述符作为复制目标。
代码：fcntl.c

```c
#include <stdio.h>
#include <unistd.h>
#include <string.h>
#include <fcntl.h>
int main(void) {
    int fd1 = open("fcntl1.txt", O_WRONLY |
        O_CREAT | O_TRUNC, 0644);
    if (fd1 == -1) {
        perror("open");
        return -1;
    }
    printf("fd1 = %d\n", fd1);
    int fd2 = open("fcntl2.txt", O_WRONLY |
        O_CREAT | O_TRUNC, 0644);
    if (fd2 == -1) {
        perror("open");
        return -1;
    }
    printf("fd2 = %d\n", fd2);
//  int fd3 = dup2(fd1, fd2);
    int fd3 = fcntl(fd1, F_DUPFD, fd2);
    if(fd3 == -1) {
        perror("dup2");
        return -1;
    }
    printf("fd3 = %d\n", fd3);
    const char* text = "123";
    if (write(fd1, text, strlen(text) * sizeof(
        text[0])) == -1) {
        perror("write");
        return -1;
    }
    text = "456";
    if (write(fd2, text, strlen(text) * sizeof(
        text[0])) == -1) {
        perror("write");
        return -1;
    }
    text = "789";
    if (write(fd3, text, strlen(text) * sizeof(
        text[0])) == -1) {
        perror("write");
        return -1;
    }
    close(fd3);
    close(fd2);
    close(fd1);
    return 0;
}
```

**获取/设置文件描述符标志**

截止目前只有一个文件描述符标志位：FD_CLOEXEC
一个进程可以通过exec函数族启动另一个进程取代其自身。
原进程中无FD_CLOEXEC标志位的文件描述符在新进程中会依然保持打开状态，这也是文件描述符的默认状态。如果原进程中的某个文件描述符带有此标志位，那么在新进程中该文件描述符会被关闭。
// 获取文件描述符标志
int fcntl(int fd, F_GETFD);
成功返回文件描述符标志，失败返回-1。
// 设置文件描述符标志
int fcntl(int fd, F_SETFD, int flags);
成功返回0，失败返回-1。
代码：fd.c

```c
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
int main(void) {
    int fd = open("fd.txt", O_RDWR | O_CREAT |
        O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    int flags = fcntl(fd, F_GETFD);
    if (flags == -1) {
        perror("fcntl");
        return -1;
    }
again:
    if (flags & FD_CLOEXEC)
        printf("文件描述符%d"
            "将在新进程中被关闭。\n", fd);
    else {
        printf("文件描述符%d"
            "将在新进程中保持打开。\n", fd);
        flags |= FD_CLOEXEC;
        if (fcntl(fd, F_SETFD, flags) == -1) {
            perror("fcntl");
            return -1;
        }
        goto again;
    }
    // 调用exec函数族创建新进程...
    close(fd);
    return 0;
}
```

**获取/追加文件状态标志**
// 获取文件状态标志
int fcntl(int fd, F_GETFL);
成功返回文件状态标志，失败返回-1。
与文件创建有关的三个状态标志：
O_CREAT/O_EXCL/O_TRUC，无法被获取。
只读标志O_RDONLY的值为0，不能与位与检测。
int flags = fcntl(fd, F_GETFL);
if ((flags & O_ACCMODE) == O_RDONLY)
    // 只读文件
if (flags & O_WRONLY)
    // 只写文件
if (flags & O_RDWR)
    // 读写文件
...
// 追加文件状态标志
int fcntl(int fd, F_SETFL, flags);
成功返回0，失败返回-1。
只有O_APPEND和O_NONBLOCK两个状态标志可被追加。
代码：fl.c

```c
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
void pflags(int flags) {
    printf("文件状态标志(%08X)：", flags);
    struct {
        int flag;
        const char* desc;
    }   flist[] = {
        {O_RDONLY,   "O_RDONLY"},
        {O_WRONLY,   "O_WRONLY"},
        {O_RDWR,     "O_RDWR"},
        {O_APPEND,   "O_APPEND"},
        {O_CREAT,    "O_CREAT"},
        {O_EXCL,     "O_EXCL"},
        {O_TRUNC,    "O_TRUNC"},
        {O_NOCTTY,   "O_NOCTTY"},
        {O_NONBLOCK, "O_NONBLOCK"},
        {O_SYNC,     "O_SYNC"},
        {O_DSYNC,    "O_DSYNC"},
        {O_RSYNC,    "O_RSYNC"},
        {O_ASYNC,    "O_ASYNC"}};
    for (size_t i = 0; i < sizeof(flist) /
        sizeof(flist[0]); ++i) {
        if (flist[i].flag == O_RDONLY) {
            if ((flags & O_ACCMODE) ==
                flist[i].flag)
                printf("%s ", flist[i].desc);
        }
        else if (flags & flist[i].flag)
            printf("%s ", flist[i].desc);
    }
    printf("\n");
}
int main(void) {
    int fd = open("fl.txt", O_RDONLY | O_CREAT |
        O_TRUNC | O_ASYNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    int flags = fcntl(fd, F_GETFL);
    if (flags == -1) {
        perror("fcntl");
        return -1;
    }
    pflags(flags);
    if (fcntl(fd, F_SETFL,
        O_RDWR | O_APPEND | O_NONBLOCK) == -1) {
        perror("fcntl");
        return -1;
    }
    if ((flags = fcntl(fd, F_GETFL)) == -1) {
        perror("fcntl");
        return -1;
    }
    pflags(flags);
    close(fd);
    return 0;
}
```

### 9.10 文件锁

**读写冲突**
hello
world
hewollrlod
                                           期望的访问
                                           读取    写入
文件的某 无人访问              OK     OK
个区域正 多人在读              OK     NO
在被访问 一人在写              NO     NO
为了避免在读写同一个文件的同一个区域时发生冲突，进程之间应该遵循以下规则：
如果一个进程正在写，那么其它进程既不能写也不能读。
如果一个进程正在读，那么其它进程不能写但是可以读。
读共享，写独占。
代码：write.c、read.c

```c
write.c
    
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
int main(int argc, char* argv[]) {
    if (argc < 2) {
        fprintf(stderr, "用法：%s <字符串>\n",
            argv[0]);
        return -1;
    }
    int fd = open("shared.txt", O_WRONLY |
        O_CREAT | O_APPEND, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    size_t len = strlen(argv[1]);
    for (size_t i = 0; i < len; ++i) {
        if (write(fd, &argv[1][i], sizeof(
            argv[1][i])) == -1) {
            perror("write");
            return -1;
        }
        sleep(1);
    }
    close(fd);
    return 0;
}

read.c
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
int main(void) {
    int fd = open("shared.txt", O_RDONLY);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    char buf[1024];
    ssize_t readed;
    while((readed = read(fd, buf, sizeof(
        buf))) > 0)
        write(STDOUT_FILENO, buf, readed);
    printf("\n");
    if (readed == -1) {
        perror("read");
        return -1;
    }
    close(fd);
    return 0;
}   
```

为了避免多个进程在读写同一个文件的同一个区域时发生冲突，操作系统引入了文件锁机制，并把文件锁分读锁和写锁两种，它们区别在于：
读锁：共享锁，对一个文件的特定区域可以同时加多个读锁
写锁：排它锁，对一个文件的特定区域只能加一把写锁
锁模式：加锁->读写->解锁
                                       期望的加锁
                                       读锁    写锁
文件的某 无任何锁         OK     OK
个区域正 多把读锁          OK     NO
在被访问 一把写锁          NO     NO

```c
int fcntl(int fd, F_SETLKW/F_SETLK, struct flock* lock);
                  ^             ^
                阻塞          非阻塞
struct flock {
    short int l_type;       // 锁类型
                                       // F_RDLCK/F_WRLCK/F_UNLCK
    short int l_whence;  // 锁区偏移起点
                                       // SEEK_SET/SEEK_CUR/
                                       // SEEK_END
    off_t        l_start;       // 锁区偏移
    off_t        l_len;          // 锁区长度(字节数)，0表示锁到尾
    pid_t        l_pid;         // 加锁进程PID，-1表示自动设置
};
```

对相对于文件头10字节开始的20字节以阻塞模式加读锁。

```c
struct flock lock;
lock.l_type = F_RDLCK;
lock.l_whence = SEEK_SET;
lock.l_start = 10;
lock.l_len = 20;
lock.l_pid = -1;
fcntl(fd, F_SETLKW, &lock);
```

对相对于当前位置10字节开始到文件尾以非阻塞方式加写锁。

```c
struct flock lock;
lock.l_type = F_WRLCK;
lock.l_whence = SEEK_CUR;
lock.l_start = 10;
lock.l_len = 0;
lock.l_pid = -1;
fcntl(fd, F_SETLK, &lock);
```

对整个文件解锁。

```c
struct flock lock;
lock.l_type = F_UNLCK;
lock.l_whence = SEEK_SET;
lock.l_start = 0;
lock.l_len = 0;
lock.l_pid = -1;
fcntl(fd, F_SETLK, &lock);
```

代码：wlock.c、rlock.c

```c
wlock.c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <errno.h>
// 加写锁
int wlock(int fd, int wait) {
    struct flock lock;
    lock.l_type   = F_WRLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = 0;
    lock.l_len    = 0;
    lock.l_pid    = -1;
    return fcntl(fd, wait ? F_SETLKW : F_SETLK,
        &lock);
}
// 解除锁
int ulock(int fd) {
    struct flock lock;
    lock.l_type   = F_UNLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = 0;
    lock.l_len    = 0;
    lock.l_pid    = -1;
    return fcntl(fd, F_SETLK, &lock);
}
int main(int argc, char* argv[]) {
    if (argc < 2) {
        fprintf(stderr, "用法：%s <字符串>\n",
            argv[0]);
        return -1;
    }
    int fd = open("shared.txt", O_WRONLY |
        O_CREAT | O_APPEND, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    /*
    if (wlock(fd, 1) == -1) {
        perror("wlock");
        return -1;
    }
    */
    while (wlock(fd, 0) == -1) {
        if (errno != EACCES && errno != EAGAIN) {
            perror("wlock");
            return -1;
        }
        printf("该文件已被锁定，稍后再试...\n");
        // 空闲处理...
    }
    size_t len = strlen(argv[1]);
    for (size_t i = 0; i < len; ++i) {
        if (write(fd, &argv[1][i], sizeof(
            argv[1][i])) == -1) {
            perror("write");
            return -1;
        }
        sleep(1);
    }
    if (ulock(fd) == -1) {
        perror("ulock");
        return -1;
    }
    close(fd);
    return 0;
}

rlock.c
    
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
#include <errno.h>
// 加读锁
int rlock(int fd, int wait) {
    struct flock lock;
    lock.l_type   = F_RDLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = 0;
    lock.l_len    = 0;
    lock.l_pid    = -1;
    return fcntl(fd, wait ? F_SETLKW : F_SETLK,
        &lock);
}
// 解除锁
int ulock(int fd) {
    struct flock lock;
    lock.l_type   = F_UNLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = 0;
    lock.l_len    = 0;
    lock.l_pid    = -1;
    return fcntl(fd, F_SETLK, &lock);
}
int main(void) {
    int fd = open("shared.txt", O_RDONLY);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    /*
    if (rlock(fd, 1) == -1) {
        perror("rlock");
        return -1;
    }
    */
    while (rlock(fd, 0) == -1) {
        if (errno != EACCES && errno != EAGAIN) {
            perror("rlock");
            return -1;
        }
        printf("该文件已被锁定，稍后再试...\n");
        // 空闲处理...
    }
    char buf[1024];
    ssize_t readed;
    while ((readed = read(fd, buf, sizeof(
        buf))) > 0)
        write(STDOUT_FILENO, buf, readed);
    /*
    char buf[1];
    ssize_t readed;
    while ((readed = read(fd, buf, sizeof(
        buf))) > 0) {
        write(STDOUT_FILENO, buf, readed);
        sleep(1);
    }
    */
    printf("\n");
    if (readed == -1) {
        perror("read");
        return -1;
    }
    if (ulock(fd) == -1) {
        perror("ulock");
        return -1;
    }
    close(fd);
    return 0;
}    

```

测试对文件的某个区域是否可以加某种锁，如果不能加锁，是什么原因导致加锁冲突？
int fcntl(int fd, F_GETLK, struct flock* lock);
成功返回0，失败返回-1。
调用该函数时，lock参数表示欲加之锁的细节。函数成功返回时，通过lock参数输出欲加之锁是否可加，以及存在冲突的锁信息。
代码：lock1.c、lock2.c

```c
lock1.c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
// 加读锁
int rlock(int fd, off_t start, off_t len,
    int wait) {
    struct flock lock;
    lock.l_type   = F_RDLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = start;
    lock.l_len    = len;
    lock.l_pid    = -1;
    return fcntl(fd, wait ? F_SETLKW : F_SETLK,
        &lock);
}
// 加写锁
int wlock(int fd, off_t start, off_t len,
    int wait) {
    struct flock lock;
    lock.l_type   = F_WRLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = start;
    lock.l_len    = len;
    lock.l_pid    = -1;
    return fcntl(fd, wait ? F_SETLKW : F_SETLK,
        &lock);
}
// 解除锁
int ulock(int fd, off_t start, off_t len) {
    struct flock lock;
    lock.l_type   = F_UNLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = start;
    lock.l_len    = len;
    lock.l_pid    = -1;
    return fcntl(fd, F_SETLK, &lock);
}
int main(void) {
    printf("进程标识(PID)：%d\n", getpid());
    int fd = open("shared.txt", O_RDWR |
        O_CREAT | O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    const char* text = "ABCDEFGHIJKLMNOPQRST";
    if (write(fd, text, strlen(text) * sizeof(
        text[0])) == -1) {
        perror("write");
        return 0;
    }
    printf("对EFGH加读锁");
    if (rlock(fd, 4, 4, 0) == -1) {
        printf("失败：%m\n");
        return -1;
    }
    printf("成功！\n");
    printf("对MNOP加写锁");
    if (wlock(fd, 12, 4, 0) == -1) {
        printf("失败：%m\n");
        return -1;
    }
    printf("成功！\n");
    printf("按<回车>，解锁MN...");
    getchar();
    ulock(fd, 12, 2);
    printf("按<回车>，解锁EFGH...");
    getchar();
    ulock(fd, 4, 4);
    close(fd);
    return 0;
}

lock2.c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
// 打印锁
void plock(struct flock lock) {
    if (lock.l_type == F_UNLCK)
        printf("没有锁。\n");
    else {
        printf("%d进程", lock.l_pid);
        switch (lock.l_whence) {
            case SEEK_SET:
                printf("在距文件头");
                break;
            case SEEK_CUR:
                printf("在距当前位置");
                break;
            case SEEK_END:
                printf("在距文件尾");
                break;
        }
        printf("%ld字节处，为%ld字节加了",
            lock.l_start, lock.l_len);
        switch (lock.l_type) {
            case F_RDLCK:
                printf("读锁。\n");
                break;
            case F_WRLCK:
                printf("写锁。\n");
                break;
        }
    }
}
// 测读锁
int rtest(int fd, off_t start, off_t len) {
    struct flock lock;
    lock.l_type   = F_RDLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = start;
    lock.l_len    = len;
    lock.l_pid    = -1;
    if (fcntl(fd, F_GETLK, &lock) == -1)
        return -1;
    plock(lock);
    return 0;
}
// 测写锁
int wtest(int fd, off_t start, off_t len) {
    struct flock lock;
    lock.l_type   = F_WRLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = start;
    lock.l_len    = len;
    lock.l_pid    = -1;
    if (fcntl(fd, F_GETLK, &lock) == -1)
        return -1;
    plock(lock);
    return 0;
}
// 加读锁
int rlock(int fd, off_t start, off_t len,
    int wait) {
    struct flock lock;
    lock.l_type   = F_RDLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = start;
    lock.l_len    = len;
    lock.l_pid    = -1;
    return fcntl(fd, wait ? F_SETLKW : F_SETLK,
        &lock);
}
// 加写锁
int wlock(int fd, off_t start, off_t len,
    int wait) {
    struct flock lock;
    lock.l_type   = F_WRLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = start;
    lock.l_len    = len;
    lock.l_pid    = -1;
    return fcntl(fd, wait ? F_SETLKW : F_SETLK,
        &lock);
}
// 解除锁
int ulock(int fd, off_t start, off_t len) {
    struct flock lock;
    lock.l_type   = F_UNLCK;
    lock.l_whence = SEEK_SET;
    lock.l_start  = start;
    lock.l_len    = len;
    lock.l_pid    = -1;
    return fcntl(fd, F_SETLK, &lock);
}
int main(void) {
    int fd = open("shared.txt", O_RDWR);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    printf("对CDEF测读锁。");
    if (rtest(fd, 2, 4) == -1) {
        printf("失败：%m\n");
        return -1;
    }
    printf("对CDEF加读锁");
    if (rlock(fd, 2, 4, 0) == -1)
        printf("失败：%m\n");
    else {
        printf("成功！\n");
        ulock(fd, 2, 4);
    }
    printf("对CDEF测写锁。");
    if (wtest(fd, 2, 4) == -1) {
        printf("失败：%m\n");
        return -1;
    }
    printf("对CDEF加写锁");
    if (wlock(fd, 2, 4, 0) == -1)
        printf("失败：%m\n");
    else {
        printf("成功！\n");
        ulock(fd, 2, 4);
    }
    printf("对KLMN测读锁。");
    if (rtest(fd, 10, 4) == -1) {
        printf("失败：%m\n");
        return -1;
    }
    printf("对KLMN加读锁");
    if (rlock(fd, 10, 4, 0) == -1)
        printf("失败：%m\n");
    else {
        printf("成功！\n");
        ulock(fd, 10, 4);
    }
    printf("对KLMN测写锁。");
    if (wtest(fd, 10, 4) == -1) {
        printf("失败：%m\n");
        return -1;
    }
    printf("对KLMN加写锁");
    if (wlock(fd, 10, 4, 0) == -1)
        printf("失败：%m\n");
    else {
        printf("成功！\n");
        ulock(fd, 10, 4);
    }
    printf("等待KLMN上的写锁被解除...\n");
    printf("对KLMN加写锁");
    if (wlock(fd, 10, 4, 1) == -1)
        printf("失败：%m\n");
    else {
        printf("成功！\n");
        ulock(fd, 10, 4);
    }
    close(fd);
    return 0;
}
```

v节点
    i节点内容
    锁表指针->锁节点->锁节点->...
    ...                   锁的类型
                          锁区偏移
                          锁区大小
                          加锁进程的PID
每次对给定文件的特定区域加锁，都会通过fcntl函数向系统内核传递flock结构，该结构中包含了有关锁的一些细节，诸如锁的类型、锁区的起始位置和大小，甚至加锁进程的PID(填-1由系统自动设置)。系统内核会收集所有进程对该文件所加的各种锁，并把这些flock结构中的信息，以链表的形式组织成一张锁表，其起始地址就保存在该文件的v节点中。任何一个进程通过fcntl函数对该文件加锁，系统内核都要遍历这张锁表，一旦发现有与欲加之锁构成冲突的锁即阻塞或报错，否则即将欲加之锁插入锁表，而解锁的过程实际上就是调整或删除锁表中的相应节点。
文件锁属于劝谏锁，亦称协议锁。

### 9.11 文件元数据

```c
i节点
    文件元数据
    数据块索引表
struct stat {
    dev_t st_dev; // 设备ID
    ino_t st_ino; // i节点号
    mode_t st_mode; // 文件类型和权限
    nlink_t st_nlink; // 硬链接数
    uid_t st_uid; // 用户ID
    gid_t st_gid; // 组ID
    dev_t st_rdev; // 特殊设备ID
    off_t st_size; // 总字节数
    blksize_t st_blksize; // I/O块字节数
    blkcnt_t st_blocks; // 块数
    time_t st_atime; // 最后访问时间
    time_t st_mtime; // 最后修改时间
    time_t st_ctime; // 最后状态改变时间
};
```

文件类型和权限的数据类型mode_t其实就是一个整数，其中只有低16位有效。
B15~B12 - 文件类型，掩码：S_IFMT(跟这个掩码作用，B11到B0这12位都屏蔽成0，剩下的就是最高的4位)
1000，S_IFREG，普通文件，-
0100，S_IFDIR，目录文件，d
1100，S_IFSOCK，本地套接字文件，s
0010，S_IFCHR，字符设备文件，c
0110，S_IFBLK，块设备文件，b
1010，S_IFLNK，符号链接文件，l
0001，S_IFFIFO，有名管道文件，p
B11~B9 - 设置用户ID位、设置组ID位和粘滞位
带有设置用户ID位(即B11位为1)的可执行文件(如：/usr/bin/passwd)：
系统中的每个进程其实都有两个用户ID，一个叫实际用户ID，取决于运行该进程的登录用户，另一个叫有效用户ID。一般情况下，进程的有效用户ID就取自其实际用户ID。但是如果产生该进程的可执行文件带有设置用户ID位，那么该进程的有效用户ID就不再取自实际用户ID，而是取自该可执行文件的拥有者用户ID。进程对系统资源的权限判定是根据其有效用户ID做出的，因此通过这种方法，就可以提升普通用户执行进程的权限，完成本来只有高权限用户才能完成的任务，即有限提权。
带有设置组ID位(即B10位为1)的可执行文件：
设置组ID位(B10)的情况与上述类似，只是针对进程的有效组ID而已。
带有设置用户ID位的不可执行文件：毫无意义。
带有设置组ID位的不可执行文件：某些系统用这种无意义的状态作为强制锁标志。
带有粘滞位(B9位为1)的目录：除root以外的任何用户在该目录下，都只能删除或更名那些属于自己的文件或子目录，而对于其它用户的文件或子目录，既不能删除也不能改名。如：/tmp

```c
B8B7B6 - 拥有者用户   宏 S_IRUSR 可读的那一位是1，其余为0 (I表示i节点，R表示读的意思)
 | | |                 S_IWUSR 可写的那一位为1，其余为0
读 写 执行               S_IXUSR 可执行的那一位是1，其余为0
B5B4B3 - 拥有者组
 | | |
读 写 执行
B2B1B0 - 其它用户
 | | |
读 写 执行
拥有者用户       拥有者组       其它用户
C1  C2  C3    C4  C5  C6   C7  C8  C9
-/r -/w -/x  -/r -/w -/x   -/r -/w -/x
设置用户ID位
        S/s
               设置组ID位
                     S/s
                                    粘滞位
                                     T/t
    
说明：如设置用户ID位往这里面合，如果是可执行的且设置了用户ID位用s表示，如果是不可执行的且设置了用户ID位用S表示。
```

**元数据怎么拿**

```c
#include <sys/stat.h>
int stat(char const* path, struct stat* buf);
int fstat(int fd, struct stat* buf);
int lstat(char const* path, struct stat* buf); // 不跟踪符号链接(拿的是符号链接本身，不是它的目标文件)
成功返回0，失败返回-1。
path - 文件路径
buf - 文件元数据结构
fd - 文件描述符
```

**注意：**stat与fstat的区别是一个给的是文件路径，一个给的是文件描述符。

代码：stat.c

```c
#include <stdio.h>
#include <string.h>
#include <sys/stat.h>
#include <time.h>
int main(int argc, char* argv[]) {
    if (argc < 2)
        goto usage;
    struct stat st;
    if (argc < 3) {
        // 跟踪符号链接
        if (stat(argv[1], &st) == -1) {
            perror("stat");
            return -1;
        }
    }
    else if (!strcmp(argv[2], "-l")) {
        // 不跟踪符号链接
        if (lstat(argv[1], &st) == -1) {
            perror("lstat");
            return -1;
        }
    }
    else
        goto usage;
    printf("设备ID：%llu\n", st.st_dev);
    printf("i节点号：%ld\n", st.st_ino);
    printf("模式：%d\n", st.st_mode);
    printf("硬链接数：%d\n", st.st_nlink);
    printf("用户ID：%d\n", st.st_uid);
    printf("组ID：%d\n", st.st_gid);
    printf("特殊设备ID：%llu\n", st.st_rdev);
    printf("总字节数：%ld\n", st.st_size);
    printf("I/O块字节数：%ld\n", st.st_blksize);
    printf("占用块数：%ld\n", st.st_blocks);
    printf("最后访问时间：%ld\n", st.st_atime);
    printf("最后修改时间：%ld\n", st.st_mtime);
    printf("最后改变时间：%ld\n", st.st_ctime);
    return 0;
usage:
    fprintf(stderr, "用法：%s <文件> [-l]\n",
        argv[0]);
    return -1;
}
```

### 9.12 访问测试

int access(const char* pathname, int mode);
成功返回0，失败返回-1。
pathname - 文件路径
mode - 访问权限，可取以下值：
R_OK: 可读否
W_OK: 可写否
X_OK: 可执行否
F_OK: 存在否
根据调用该函数的进程的实际用户ID和实际组ID，检测其是否可读、可写或可执行给定的文件，也可检测该文件是否存在。
代码：access.c

```c
#include <stdio.h>
#include <unistd.h>
int main(int argc, char* argv[]) {
    if (argc < 2) {
        fprintf(stderr, "用法：%s <文件>\n",
            argv[0]);
        return -1;
    }
    printf("文件%s", argv[1]);
    if (access(argv[1], F_OK) == -1)
        printf("不存在(%m)。\n");
    else {
        if (access(argv[1], R_OK) == -1)
            printf("不可读(%m)，");
        else
            printf("可读，");
        if (access(argv[1], W_OK) == -1)
            printf("不可写(%m)，");
        else
            printf("可写，");
        if (access(argv[1], X_OK) == -1)
            printf("不可执行(%m)。\n");
        else
            printf("可执行。\n");
    }
    return 0;
}
```

### 9.13 权限掩码

#include <sys/stat.h>
mode_t umask(mode_t cmask);
永远成功，返回原来的权限掩码。
cmask - 新权限掩码
权限掩码是进程的属性之一，存储在系统内核中的进程表项里。umask函数所影响的仅仅是调用进程自己，对于其它进程，包括其父进程，如Shell，都没有任何影响。

### 9.14 修改权限

#include <sys/stat.h>
int chmod(const char* pathname, mode_t mode);
int fchmod(int fd, mode_t mode);
成功返回0，失败返回-1。
pathname - 文件路径
mode - 文件权限
fd - 文件描述符
调用进程的有效用户ID必须与文件的拥有者用户ID匹配，或者是root用户，才能修改该文件的权限，且受权限掩码的影响。

### 9.15 修改文件的拥有者和(或)拥有者组

#include <unistd.h>
int chown(const char* path, uid_t owner, gid_t group);
int fchown(int fd, uid_t owner, gid_t group);
int lchown(const char* path, uid_t owner,
    gid_t group); // 不跟踪符号链接
成功返回0，失败返回-1。
path - 文件路径
owner - 拥有者用户ID，-1表示不修改 
group - 拥有者组ID，-1表示不修改
fd - 文件描述符
如果调用进程的有效用户ID为root用户，则它可以任意修改任何文件的拥有者用户和组。如果调用进程的有效用户ID为普通用户，则它只能把自己名下文件的拥有者组改成自己隶属的其它组。root的ID为0。
代码：chown.c

```c
#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
int main(void) {
    int fd = open("chown.txt", O_RDWR | O_CREAT |
        O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    if (fchown(fd, 0, 0) == -1) {
        perror("fchown");
        return -1;
    }
    close(fd);
    return 0;
}
```

### 9.16 修改文件大小

#include <unistd.h>
int truncate(const char* path, off_t length);
int ftruncate(int fd, off_t length);
成功返回0，失败返回-1。
path - 文件路径
length - 文件大小
fd - 文件描述符
大->小：截掉靠文件尾的部分。
小->大：在文件尾之后增加0。
代码：trunc.c

```c
```

struct Student {
    char name[128];
    int age;
    float score;
    ...
};
| 学生1 | 学生2 | 学生4 | 学生5 |
                                     ^
mmap/munmap
虚拟内存<->磁盘文件
#include <sys/mman.h>
void* mmap(void* start, size_t length, int prot,
    int flags, int fd, off_t offset);
             ^
              |
   MAP_SHARED
fd - 文件描述符
offset - 文件内偏移量，自动按页对齐(给4096以内的数据都按0算，要是给4096到8192之间的数都按4096算)
代码：mmap1.c、mmap2.c

```c
mmap1.c

#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/mman.h>
int main(void) {
    char const* text = "Hello, World!";
    size_t size = strlen(text) * sizeof(
        text[0]);
    int fd = open("mmap.txt", O_RDWR | O_CREAT |
        O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    if (ftruncate(fd, size) == -1) {
        perror("ftruncate");
        return -1;
    }
    void* map = mmap(NULL, size, PROT_WRITE,
        MAP_SHARED, fd, 0);
    if (map == MAP_FAILED) {
        perror("mmap");
        return -1;
    }
    memcpy(map, text, size); // 写文件
    munmap(map, size);
    close(fd);
    return 0;
}

mmap2.c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/stat.h>
#include <sys/mman.h>
int main(void) {
    int fd = open("mmap.txt", O_RDONLY);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    struct stat st;
    if (fstat(fd, &st) == -1) {
        perror("fstat");
        return -1;
    }
    void* map = mmap(NULL, st.st_size, PROT_READ,
        MAP_SHARED, fd, 0);
    if (map == MAP_FAILED) {
        perror("mmap");
        return -1;
    }
    char text[st.st_size + 1];
    memcpy(text, map, st.st_size);
    text[st.st_size] = '\0';
    printf("%s\n", text);
    munmap(map, st.st_size);
    close(fd);
    return 0;
}
```

### 9.17 硬链接

硬链接就是文件路径，即由各级目录、分隔符(/)和文件名共同组成的字符串，与一个特定的i节点号所形成的对应关系。

ln <目标路径(已经存在的路径)> <源路径(新建立的路径)>

**根据一个已有的硬链接创建一个新的硬链接**

```c
int link(const char* oldpath, const char* newpath);
成功返回0，失败返回-1。
oldpath - 已有的硬链接路径
newpath - 新的硬链接路径
oldpath->i节点编号<->newpath
      \_____________/
             |
          目录文件
oldpath必须存在，newpath中不能包含不存在目录。
```

**删除硬链接**

```c
int unlink(const char* pathname);
成功返回0，失败返回-1。
pathname - 文件路径(不能是目录)
从pathname所对应的目录文件中删除包含该文件的条目，同时将其对应的i节点中的硬链接数减一，若该硬链接数被减至0，则将该文件所占用的磁盘空间释放出来。
```

**修改硬链接**

```c
int rename(const char* oldpath,
    const char* newpath);
成功返回0，失败返回-1。
oldpath - 原路径
newpath - 新路径
原路径->i节点编号<->新路径
    \_________/\___________/
            |                  |
         删除        目录文件
rename("./a.txt", "./b.txt"); // 改名
rename("a/1.txt", "b/1.txt"); // 移动
rename("a/1.txt", "b/2.txt"); // 移动且改名
另一个版本的unlink，还可以删除空目录的硬链接：
int remove(const char* pathname);
```

代码：link.c、unlink.c、remove.c、rename.c

```c
```

### 9.18 软链接

软链接文件的本质就是保存着另一个文件或目录的路径的文件。
根据一个已有的硬链接创建一个符号链接
int symlink(const char* oldpath,
    const char* newpath);
成功返回0，失败返回-1。
oldpath - 原有路径，可以是文件，也可以是目录，甚至可以不存在
newpath - 新建路径，不能包含不存在目录

**读取软链接文件本身的内容**
ssize_t readlink(const char* path, char* buf,
    size_t size);
成功返回拷入buf的符号链接文件内容的字节数，失败返回-1。
path - 软链接文件路径
buf - 缓冲区
size - 缓冲区大小
代码：slink.c

```c
```

### 9.19 目录

**创建一个空目录**
#include <sys/stat.h>
int mkdir(const char* pathname, mode_t model);
成功返回0，失败返回-1。
pathname - 目录路径
mode - 访问权限，目录的执行权限(x)表示可以进入
**删除一个空目录**
int rmdir(const char* pathname);
成功返回0，失败返回-1。
pathname - 目录路径
remove = unlink + rmdir
代码：dir.c

```c
```

**获取当前工作目录**
char* getcwd(char* buf, size_t size);
成功返回工作目录字符串指针，即buf，失败返回NULL。
buf - 缓冲区
size - 缓冲区大小
自动追加结尾空字符。
当前工作目录作为进程的属性之一，也是系统内核进程表项的一部分。

**改变当前工作目录**
int chdir(const char* path);
成功返回0，失败返回-1。
代码：dir.c

```c
```

**打开目录**
DIR* opendir(const char*name);
成功返回目录流指针，失败返回NULL。

**读取目录**

```c
struct dirent* readdir(DIR* dirp);
成功返回目录条目指针，读完(不设置errno)或失败(设置errno)返回NULL。
struct dirent {
    ino_t                  d_ino;            // 节点号
    off_t                   d_off;           // 下一条位置(索引)
    unsigned short d_reclen;     // 记录长度
    unsigned char   d_type;       // 文件类型
    char                     d_name[]; // 文件名
};
```

**关闭目录流**
int closedir(DIR* dirp);
成功返回0，失败返回-1。
dirp - 目录流指针
代码：list.c

```c
```

##  十、进程

### 10.1 进程的基本概念

程序：磁盘上的可执行文件。
                    | 加载
                    v
进程：内存中的指令和数据。
                    执行 |         ^
                            v          | 访问
                         CPU------+

### 10.2 进程的分类

交互式进程：由Shell启动，借助标准I/O与用户交互。
批处理进程：在无需人工干预的条件下，自动运行一组批量任务。
守护(精灵)进程：后台服务，多数时候处于待命状态，一旦有需要可被激活完成特定的任务。

### 10.3 进程快照

```c
ps - 显示当前用户拥有控制终端的进程信息
ps axuw - BSD风格选项
a: 所有用户
x: 既包括有控制终端也包括无控制终端的进程
u: 详细信息
w: 更大列宽
ps -efFl - SVR4风格选项
e: 所有用户的所有进程
f: 完整格式
F: 更完整格式
l: 长格式
USER/UID: 进程的实际用户ID
PID: 进程标识
%CPU/C: CPU使用率
%MEM: 内存使用率
VSZ: 占用虚拟内存大小(KB)
RSS: 占用半导体物理内存大小(KB)
TTY: 终端次设备号
         ttyn - 物理终端(硬件设备)
         pts/n - 虚拟终端(软件窗口)
         ? - 无控制终端，如后台进程
STAT/S: 进程状态
               O - 就绪，等待被调度
               R - 运行，Linux下没有O状态，就绪状态也用R表示
               S - 可唤醒睡眠，系统中断，获得资源，收到信号，
                    都可被唤醒，转入运行状态
               D - 不可唤醒的睡眠
                     只能被wake_up系统调用唤醒
               T - 暂停，收到SIGSTOP(19)信号转入暂停状态，
                     收到SIGCONT(18)信号转入运行状态
               W - 等待内存分页(2.6内核后被废弃)
                X - 终止且被回收，不可见
                Z - 僵尸，已退出但未被回收
                < - 高优先级
                N - 低优先级
                L - 有被锁定在半导体内存中的分页
                s - 会话首进程
                l - 多线程化
                + - 在前台进程组中
START: 进程启动时间
TIME: 进程运行时间
COMMAND/CMD: 进程启动命令
F: 进程标志
    1 - 通过fork产生的子进程，但是
         并没有通过exec创建新进程
    4 - 拥有超级用户(root)特权
PPID: 父进程的PID
NI: 进程nice值，-20~19，进程优先级浮动量
PRI: 进程优先级=80+nice，60~99，值越小优先级越高
         I/O消耗型进程，奖励，提高优先级，降低nice值
         处理机消耗型进程，惩罚，降低优先级，提高nice值
ADDR: 内核进程的内存地址，普通进程显示"-"
SZ: 占用虚拟内存页数
WCHAN: 进程正在等待的内核函数或事件
PSR: 进程当前正在被哪个处理器执行
```

### 10.4 父子孤尸

父进程创建子进程，子进程继承父进程。
一个父进程可以创建多个子进程，每个子进程有且仅有一个父进程，除非是根进程(PID=0，调度器实例)没有父进程。

```c
进程树：
调度进程(PID=0)
    init(PID=1)
        xinetd
            in.telnetd    <-    远程登录
                 login           <-   用户名和口令
                     bash             <-   Shell命令：ls
                          ls                    ->   显示目录条目清单
```

父进程在创建完子进程以后依然存在，甚至可以和子进程进行某种形式的交互，如：传参、回收、通信等。
旧进程在创建完新进程以后被其取代，新进程沿用旧进程的PID，继续独立地存在。
父进程创建子进程以后，子进程在操作系统的调度下与其父进程同时运行。如果父进程先用子进程的终止而终止，子进程即成为孤儿进程，同时被init进程收养，即成为init进程的子进程，因此init进程又被成为孤儿院进程。一个进程成为孤儿进程是正常的，系统中大多数守护进程都是孤儿进程。
如果子进程先于父进程的终止而终止，但父进程由于某种原因，没有回收子进程的尸体(终止状态)，子进程即成为僵尸进程。僵尸进程虽然已经不再活动，但其终止状态和PID仍然被保留，也会占用系统资源，直到其被父进程或init进程回收为止。如果父进程直到其终止都没有回收其处于僵尸状态的子进程，init进程会立即回收这些僵尸。因此一个进程不可能同时既是僵尸进程又是孤儿进程。

### 10.5 进程的各种ID

系统内核会为每个进程维护一个进程表项，其中包括如下ID：
进程ID：系统为每个进程分配的唯一标识。内核在分配进程ID时，会持续增加，直到不发在增加了，再从头寻找被释放的ID，即延迟重用。
父进程ID：父进程的PID，在创建子进程的过程中被初始化到子进程的进程表项中。
实际用户ID：启动该进程的用户ID。
实际组ID：启动该进程的用户组ID。
有效用户ID：通常情况下，取自进程的实际用户ID。如果该进程的可执行文件带有设置用户ID位，那么该进程的有效用户ID就取自其可执行文件的拥有者用户ID。
有效组ID：通常情况下，取自进程的实际组ID。如果该进程的可执行文件带有设置组ID位，那么该进程的有效组ID就取自其可执行文件的拥有者组ID。
一个进程的能力和权限，由其有效用户ID和有效组ID决定。

```c
#include <unistd.h>
pid_t getpid(void);   // 返回调用进程的PID
pid_t getppid(void); // 返回调用进程的PPID，
                                    // 即其父进程的PID
uid_t getuid(void);   // 返回调用进程的实际用户ID
uid_t getgid(void);   // 返回调用进程的实际组ID
uid_t geteuid(void); // 返回调用进程的有效用户ID
uid_t getegid(void); // 返回调用进程的有效组ID
```

代码：id.c

```c
#include <stdio.h>
#include <unistd.h>
int main(void) {
    printf("    进程ID: %d\n", getpid());
    printf("  父进程ID: %d\n", getppid());
    printf("实际用户ID: %d\n", getuid());
    printf("  实际组ID: %d\n", getgid());
    printf("有效用户ID: %d\n", geteuid());
    printf("  有效组ID: %d\n", getegid());
    return 0;
}
```

### 10.6 创建子进程

产生进程分支(fork)
pid_t fork(void);
成功分别父子进程中返回子进程的PID和0，失败返回-1。
调用一次返回两次：
在父进程中返回所创建子进程的PID，而在子进程中返回0
函数的调用者往往可以根据该函数返回值的不同，分别为父子进程编写不同的处理分支
pid_t pid = fork();
if (pid == -1) {
    perror("fork");
    exit(EXIT_FAILURE);
}
if (pid == 0) {
    子进程的处理分支
    exit(EXIT_SUCCESS);
}
父进程的处理分支
exit(EXIT_SUCCESS);
子进程是父进程不完全副本，子进程的数据区、BSS区、堆栈区(包括I/O缓冲区)，甚至命令行参数和全景变量区都从父进程拷贝，唯有代码区与父进程共享。
fork函数成功返回以后，父子进程各自独立地运行，其被调度的先后顺序并不确定，某些实现可以保证子进程先被调度。
fork函数成功返回以后，系统内核为父进程维护的文件描述符表也被复制到子进程的进程表项中，文件表项并不复制。

```c
父进程的进程表项
    文件描述符表
        ...
        7 | ... | * -----------> 文件表项(读写位置)
| fork                               ^
v                                    |
子进程的进程表项                        |
    文件描述符表                        |
        ...                           |
        7 | ... | * ------------------+
系统总线程数达到上限(/proc/sys/kernel/threads-max)或用户总进程数达到上限(ulimit -u)，fork函数将返回失败。
一个进程如果希望创建自己的副本并执行同一份代码，或希望与另一个进程并发地运行，都可以使用fork函数。
     (父)进程
         |
       __^__fork
      /     \
   父进程   子进程
      \_____/
         |
      代码共享
      数据复制

      (父)进程
         |
       __^__fork/vfork
      /     \
      |    子进程
    父进程    | exec(另一个程序)
      |     新进程
      \_____/
          |
       数据和代码
       都是独立的
```

代码：fork.c、mem.c、os.c、is.c、ftab.c、orphan.c、zombie.c

```c
fork.c
#include <stdio.h>
#include <unistd.h>
int main(void) {
    printf("%d进程：我要调用fork()了...\n",
        getpid());
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        printf("%d进程：我是%d进程的子进程。\n",
            getpid(), getppid());
        return 0;
    }
    printf("%d进程：我是%d进程的父进程。\n",
        getpid(), pid);
    sleep(1);
    return 0;
}

mem.c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
int global = 100; // 数据区
int main(void) {
    int local = 200; // 栈区
    int* heap = malloc(sizeof(int));
    *heap = 300; // 堆区
    printf("父进程：%d %d %d\n",
        global, local, *heap);
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        printf("子进程：%d %d %d\n",
            ++global, ++local, ++*heap);
        free(heap);
        return 0;
    }
    sleep(1);
    printf("父进程：%d %d %d\n",
        global, local, *heap);
    free(heap);
    return 0;
}

os.c
#include <stdio.h>
#include <unistd.h>
int main(void) {
    printf("ABC");
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        printf("XYZ\n");
        return 0;
    }
    sleep(1);
    printf("\n");
    return 0;
}

is.c
#include <stdio.h>
#include <unistd.h>
int main(void) {
    printf("父进程：");
    int a, b, c;
    scanf("%d%d%d", &a, &b, &c);
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        scanf("%d%d%d", &a, &b, &c);
        printf("子进程：%d %d %d\n", a, b, c);
        return 0;
    }
    sleep(1);
    printf("父进程：%d %d %d\n", a, b, c);
    return 0;
}

ftab.c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
int main(void) {
    int fd = open("ftab.txt", O_RDWR | O_CREAT |
        O_TRUNC, 0644);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    char const* text = "Hello, World!";
    if (write(fd, text, strlen(text) * sizeof(
        text[0])) == -1) {
        perror("write");
        return -1;
    }
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        if (lseek(fd, -6, SEEK_CUR) == -1) {
            perror("lseek");
            return -1;
        }
        close(fd);
        return 0;
    }
    sleep(1);
    text = "Linux";
    if (write(fd, text, strlen(text) * sizeof(
        text[0])) == -1) {
        perror("write");
        return -1;
    }
    close(fd);
    return 0;
}

orphan.c
#include <stdio.h>
#include <unistd.h>
int main(void) {
    printf("%d进程：我要调用fork()了...\n",
        getpid());
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        sleep(1);
        printf("\n%d进程：我是被%d进程收养"
            "的孤儿进程。", getpid(), getppid());
        return 0;
    }
    printf("%d进程：我是%d进程的父进程，"
        "马上死！\n", getpid(), pid);
    return 0;
}

zombie.c
#include <stdio.h>
#include <unistd.h>
int main(void) {
    printf("%d进程：我要调用fork()了...\n",
        getpid());
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        printf("%d进程：我是%d进程的子进程，"
            "马上变僵尸！\n", getpid(),
            getppid());
        return 0;
    }
    sleep(1);
    printf("%d进程：我是%d进程的父进程。\n",
        getpid(), pid);
    getchar();
    return 0;
}
```

### 10.7 创建轻量级子进程

pid_t vfork(void);
成功分别父子进程中返回子进程的PID和0，失败返回-1。
vfork函数与fork函数的功能基本相同，只有一下两点区别：
1)vfork函数创建的子进程不复制父进程的物理内存，也不拥有自己独立的内存映射，而是与父进程共享全部地址空间。
2)vfork函数在创建子进程的同时会挂起父进程，直到子进程终止，或通过exec函数创建新进程，在恢复父进程的运行。
使用了写时拷贝(copy-on-write)优化技术的fork结合exec的使用，其性能并不弱于典型vfork+exec的用法。
终止vfork函数创建的子进程，不要在使用main函数中使用return语句，也不要在任何函数中调用exit函数，而要调用_exit函数，以避免对父进程造成不利影响。
代码：vfork.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
int global = 100; // 数据区
int main(void) {
    int local = 200; // 栈区
    int* heap = malloc(sizeof(int));
    *heap = 300; // 堆区
    printf("父进程：%d %d %d\n",
        global, local, *heap);
    pid_t pid = vfork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        printf("子进程：%d %d %d\n",
            ++global, ++local, ++*heap);
        //free(heap);
        //return 0;
        _exit(0);
    }
    //sleep(1);
    printf("父进程：%d %d %d\n",
        global, local, *heap);
    free(heap);
    return 0;
}
```

### 10.8 进程的终止

1)正常终止
从main函数中返回(从main函数中返回才叫终止，其他函数的返回就叫返回)
在任何地方调用exit/_exit/_Exit函数
在主线程中调用pthread_exit函数
进程一旦终止，被终止进程在用户空间所持有的资源会被自动释放，如代码区、数据区、堆栈区等，但内核空间中与该进程相关的资源，如进程表项、文件描述符等未必会得到释放。
int main(...) {
    ...
    return 0;
}
main函数的返回值和exit/_exit/_Exit函数的参数一样，构成了进程的退出码，可以被终止进程的父进程通过wait或waitpid函数获得，其中只有最低8位可被获取。
注册退出处理函数：

```c
#include <stdlib.h>
int atexit(void (*function)(void));
int on_exit(void (*function)(int, void*), void* arg);
                             ^      ^            |
                             |      |____________|
                        main函数的返回值
                        exit函数的参数
宏：
EXIT_SUCCESS - 0
EXIT_FAILURE - -1
exit函数的执行过程：
 1.调用实现通过atexit/on_exit函数注册的退出处理函数(可以注册多个函数，按注册顺序的相反顺序被调用)
 2.冲刷并关闭所有仍处于打开状态的标准I/O流
 3.删除所有通过tmpfile函数创建临时文件
 4.调用_exit函数：
      关闭所有仍处于打开状态的文件描述符(并不一定真的关，有可能别的文件还在用，引用计数会减一)
      将调用进程的子进程(无论活死)托付给孤儿院进程收养
      向调用进程的父进程发送SIGCHLD(17)信号
      令调用进程终止
_Exit与_exit的功能完全一致，唯一的区别是前者有标准库提供，被声明于stdlib.h，而后者有系统调用提供，被声明于unistd.h。
在main函数中执行return语句就相当于调用了exit函数。
```

代码：exit.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
void doexit1(void) {
    printf("doexit1()...\n");
}
void doexit2(int status, void* arg) {
    printf("doexit2(%d,%s)...\n",
        status, (char*)arg);
}
int foo(void) {
    printf("foo()...\n");
    //exit(EXIT_SUCCESS);
    //_exit(EXIT_FAILURE);
    _Exit(EXIT_FAILURE);
    return 10;
}
int main(void) {
    atexit(doexit1);
    on_exit(doexit2, "再见");
    printf("foo()函数返回%d。\n",
        foo());
    return 123;
}
```

2)异常终止
在主线程外部通过pthread_cancel将主线程取消
通过信号杀死进程
代码：loop.c
Ctrl+C -> SIGINT(2), 终端中断符信号, 进程收到该信号执行默认动作——（异常）终止。
Ctrl+\ -> SIGQUIT(3), 终端退出符信号
SIGKILL(9)
SIGTERM(15)
SIGSEGV(11), 内存段出错
SIGBUS(7), 硬件错误
...

### 10.9 回收子进程

通过等待子进程结束实现某种进程间的同步。
获知子进程的退出码，根据子进程不同的退出原因采取不同的对策。
避免过多的子进程僵尸拖垮系统。
#include <sys/wait.h>
pid_t wait(int* status);
成功返回所回收子进程的PID，失败返回-1。
status - 输出子进程的终止状态，可置NULL。
父进程在创建若干子进程以后调用wait函数：
1)若所有子进程都在运行，则阻塞，直到有子进程终止才返回
2)若至少有一个子进程已经终止，则立即返回该子进程的PID并通过status参数(若非NULL)输出器终止状态
3)若没有需要等待的活动子进程，也没有需要回收的死亡子进程，则返回-1，同时置errno为ECHILD
分析进程的终止状态：
WIFEXITED(status) - 非零表示进程正常终止
    WEXITSTATUS(status) - 进程main函数的返回值或者
    传递给exit/_exit/_Exit函数参数的低8位
WIFSIGNALED(status) - 非零表示进程被信号杀死
    WTERMSIG(status) - 杀死进程的信号编号
代码：wait1.c、wait2.c

```c
wait1.c
    
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>
int main(void) {
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        int retval = 0x12345678;
        printf("子进程：我是%d进程。"
            "我要以%#x返回值退出。\n",
            getpid(), retval);
        //return retval;
        //exit(retval);
        //_exit(retval);
        _Exit(retval);
    }
    printf("父进程：我要等待子进程...\n");
    int status;
    pid = wait(&status);
    if (WIFEXITED(status))
        printf("父进程：发现%d子进程"
            "以%#x退出码终止。\n",
            pid, WEXITSTATUS(status));
    return 0;
}

wait2.c
    
#include <stdio.h>
#include <errno.h>
#include <unistd.h>
#include <sys/wait.h>
int main(void) {
    for (int i = 0; i < 3; ++i) {
        pid_t pid = fork();
        if (pid == -1) {
            perror("fork");
            return -1;
        }
        if (pid == 0) {
            printf("子进程：我是%d进程。"
                "我要退出了。\n", getpid());
            return 0;
        }
    }
    for (;;) {
        printf("父进程：我要等待子进程...\n");
        pid_t pid = wait(NULL);
        if (pid == -1) {
            if (errno != ECHILD) {
                perror("wait");
                return -1;
            }
            printf("父进程：已经没有"
                "子进程可等了。\n");
            break;
        }
        printf("父进程：发现%d进程退出了。\n",
            pid);
    }
    getchar();
    return 0;
}
```

```c
pid_t waitpid(pid_t pid, int* status, int options);
成功返回所回收子进程的PID，失败返回-1。
pid - 进程标识，可取以下值：
<-1: 等待并回收由-pid所标识的进程组中任意子进程
-1: 等待并回收任意子进程，相当于wait函数
0: 等待并回收与调用进程同组的任意子进程
>0: 等待并回收由pid所标识的特定子进程
status - 输出子进程的终止状态，可置NULL。
options - 选项，可取以下值：
0: 阻塞模式，等不来就死等，类似于wait函数
WNOHANG: 非阻塞模式，所等子进程仍在运行，则返回0
```

代码：waitpid1.c、waitpid2.c

```c
waitpid1.c

#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
int main(void) {
    pid_t pids[3];
    for (int i = 0; i < sizeof(pids) / sizeof(
        pids[0]); ++i) {
        if ((pids[i] = fork()) == -1) {
            perror("fork");
            return -1;
        }
        if (pids[i] == 0) {
            printf("子进程：我是%d进程。"
                "我要退出了。\n", getpid());
            return 0;
        }
    }
    for (int i = 0; i < sizeof(pids) / sizeof(
        pids[0]); ++i) {
        printf("父进程：我要等待%d进程...\n",
            pids[i]);
        pid_t pid = waitpid(pids[i], NULL, 0);
        if (pid == -1) {
            perror("waitpid");
            return -1;
        }
        printf("父进程：发现%d进程退出了。\n",
            pid);
    }
    return 0;
}

waitpid2.c
    
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <errno.h>
int main(void) {
    for (int i = 0; i < 3; ++i) {
        pid_t pid = fork();
        if (pid == -1) {
            perror("fork");
            return -1;
        }
        if (pid == 0) {
            printf("子进程：我是%d进程。"
                "我要退出了。\n", getpid());
            return 0;
        }
    }
    for (;;) {
        pid_t pid = waitpid(-1, NULL, WNOHANG);
        if (pid == -1) {
            if (errno != ECHILD) {
                perror("waitpid");
                return -1;
            }
            printf("父进程：子进程都死光了。\n");
            break;
        }
        if (pid)
            printf("父进程："
                "发现%d进程退出了。\n", pid);
        else {
            printf("父进程："
                "暂时没有子进程可回收。\n");
            // 空闲处理...
        }
    }
    return 0;
} 
```

### 10.10 创建新进程

子进程：父子同在——并行。
新进程：以新换旧——取代。
exec函数族包括6个函数，根据参数的形式和是否使用PATH环境变量进行区分。

```c
为一个函数传递不定数量的字符串参数：
void foo(const char* arg, ...); // 变长参数表
foo(NULL);
foo("abc", NULL);
foo("abc", "def", NULL);
foo("abc", "def", ..., NULL);
void bar(const char* arg[]); // 字符指针数组
const char* a[] = {NULL};
bar(a);
const char* a[] = {"abc", NULL};
bar(a);
const char* a[] = {"abc", "def", NULL};
bar(a);
const char* a[] = {"abc", "def", ..., NULL};
bar(a);
#include <unistd.h>
int execl(const char* path, const char* arg, ...);
                       /       \_________________/
                      /                  |
                可执行文件的路径        命令行参数
$ gcc hello.c -o hello
gcc->argv[0]
hello.c->argv[1]
-o->argv[2]
hello->argv[3]
execl("/usr/bin/gcc", "gcc"(只为了占位), "hello.c", "-o", "hello",
     NULL);             |                    |       |       |
                      argv[0]             argv[1] argv[2] argv[3]
用arg, ...作为命令行参数，运行path所表示的可执行文件，创建新进程，并用新进程取代调用进程。成功不返回，失败返回-1。
int execlp(const char* file, const char* arg, ...);
通过file参数传入可执行文件的名字即可，无需带路径，该函数会遍历PATH环境变量中的所有路径，寻找可执行文件。
execlp("gcc", "gcc", "hello.c", "-o", "hello", NULL);
int execle(const char* path, const char* arg, ...,
    char* const envp[]);
                |
             环境变量
以NULL结尾的字符指针数组
int execv(const char* path, char* const argv[]);
char* const a[] = {"gcc", "hello.c", "-o", "hello", NULL}                                                |        |        |       |
                   argv[0]  argv[1] argv[2] argv[3]
execv("/usr/bin/gcc", a);
int execvp(const char* file, char* const argv[]);
int execve(const char* path, char* const argv[],
    char* const envp[]);
exec(执行)+
l - list，以变长参数表的形式传入命令行参数
p - path，使用PATH环境变量寻找可执行文件
e - environ，以字符指针数组的形式传入环境变量
v - vector，以字符指针数组的形式传入命令行参数

```

代码：argenv.c、exec.c

```c
argenv.c
    
#include <stdio.h>
#include <unistd.h>
void pargv (char* argv[]) {
    printf("--- 命令行参数 ---\n");
    while (argv && *argv)
        printf("%s\n", *argv++);
    printf("------------------\n");
}
void penvp(char* envp[]) {
    printf("---- 环境变量 ----\n");
    while(envp && *envp)
        printf("%s\n", *envp++);
    printf("------------------\n");
}
int main(int argc, char* argv[], char* envp[]) {
    printf("argenv的PID：%d\n", getpid());
    pargv(argv);
    penvp(envp);
    return 0;
}

exec.c
    
#include <stdio.h>
#include <stdio.h>
#include <unistd.h>
int main(void) {
    printf("exec的PID：%d\n", getpid());
    printf("准备调用exec函数...\n");
    /*
    if (execl("./argenv", "argenv", "ABC",
        "123", "Hello, World!", NULL) == -1) {
        perror("execl");
        return -1;
    }
    */
    char* argv[] = {"argenv", "ABC", "123",
        "Hello, World!", NULL};
    /*
    if (execv("./argenv", argv) == -1) {
        perror("execv");
        return -1;
    }
    */
    char* envp[] = {"NAME=minwei",
        "SCHOOL=tarena", NULL};
    /*
    if (execle("./argenv", "argenv", "ABC",
        "123", "Hello, World!", NULL,
        envp) == -1) {
        perror("execle");
        return -1;
    }
    *//*
    if (execve("./argenv", argv, envp) == -1) {
        perror("execve");
        return -1;
    }
    *//*
    if (execlp("argenv", "argenv", "ABC",
        "123", "Hello, World!", NULL) == -1) {
        perror("execlp");
        return -1;
    }
    */
    if (execvp("argenv", argv) == -1) {
        perror("execvp");
        return -1;
    }
    printf("exec函数返回成功！\n");
    return 0;
}
```

与fork或vfork函数不同，exec函数不是创建调用进程的子进程，而是创建一个新的进程取代调用进程自身。新进程会用自己的全部地址空间，覆盖调用进程的地址空间，但进程的PID保持不变。调用exec函数不仅改变调用进程的地址空间和进程映像，调用进程的一些属性也发生了变化：
1)任何处于阻塞状态的信号都会丢失；
2)被设置为捕获的信号会还原为默认操作；
3)有关线程属性的设置会还原为缺省值；
4)有关进程的统计信息会复位；
5)与进程内存有关的任何数据都会丢失，包括内存映射文件；
6)标注库在用户空间维护的一切数据结构，如通过malloc函数族动态分配的堆内存，通过atexit/on_exit函数注册的退出处理函数等，都会丢失。
但有些属性会被新进程继承下来，比如PID、PPID、实际用户ID和实际组ID、优先级，以及文件描述符(除非该文件描述符带有FD_CLOEXEC标志位)等。
vfork+exec模式：
调用exec函数固然可以创建出新的进程，但是新进程会取代原来的进程。如果既想创建新的进程，同时有希望原来的进程继续存在，则可以考虑使用vfork+exec模式，即在由vfork产生的子进程中调用exec函数，新进程取代了子进程，但父进程依然存在。
代码：ve1.c、ve2.c

```c
ve1.c
    
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <unistd.h>
int main(void) {
    srand(time(NULL));
    setbuf(stdout, NULL);
    for (int i = 0; i < 100; ++i) {
        printf("+");
        usleep((rand() % 100) * 1000);
    }
    return 0;
}

ve2.c
    
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <unistd.h>
#include <sys/wait.h>
int main(void) {
    srand(time(NULL));
    setbuf(stdout, NULL);
    pid_t pid = vfork();
    //pid_t pid = fork();
    if (pid == -1) {
        perror("vfork");
        return -1;
    }
    if (pid == 0) {
        if (execl("./ve1", "ve1", NULL) == -1) {
            perror("execl");
            _exit(-1);
        }
        /*
        for (int i = 0; i < 100; ++i) {
            printf("+");
            usleep((rand() % 100) * 1000);
        }
        _exit(0);
        */
    }
    for (int i = 0; i < 100; ++i) {
        printf("-");
        usleep((rand() % 100) * 1000);
    }
    if (waitpid(pid, NULL, 0) == -1) {
        perror("waitpid");
        return -1;
    }
    return 0;
}
```

如果一个进程可以根据用户的输入创建不同的进程，并在所建
进程结束以后继续重复这个过程，那么这个进程就是Shell：

```c
     Shell进程(bash)
+->显示提示符并
|      等待用户输入
|            | <- 输入：ls -l
|            v
|       调用vfork函
|       数创建子进程 ---------------> 子进程(ls)
|            |                         |
|            v                         v
|   调用waitpid函数              根据用户的输入调用exec 
|   等待并回收子进程 <----------- 函数创建新进程(/bin/ls)  
|            | 
+------------+
```



#include <stdlib.h>
int system(const char* command);
成功返回command命令行进程的终止状态，失败返回-1。
command - 命令行字符串
如果调用vfork或waitpid函数出错，返回-1。
如果调用exec函数出错，返回127。
如果都成功，返回command进程的终止状态，由waitpid函数的status参数输出。
如果command参数取NULL指针，该函数返回-1表示失败，返回其它非0(真)值表示当前Shell可用，返回0(假)表示不可用。
代码：sys1.c、sys2.c
system=vfork+exec+waitpid

```c
sys1.c

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/wait.h>
int main(void) {
    int status;
    if ((status = system(NULL)) == -1) {
        perror("system");
        return -1;
    }
    if (status)
        printf("Shell可用。\n");
    else {
        printf("Shell不可用。\n");
        return -1;
    }
    if ((status = system("ls -l")) == -1) {
        perror("system");
        return -1;
    }
    printf("退出码：%d\n", WEXITSTATUS(status));
    if ((status = system("ps u")) == -1) {
        perror("system");
        return -1;
    }
    printf("退出码：%d\n", WEXITSTATUS(status));
    return 0;
}

sys2.c
    
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
/*
 * command: ls -l
 * argv ->  *  -> ls
 *          *  -> -l
 *         NULL
 */
char** getargv(const char* command) {
    char cmd[strlen(command) + 1];
    strcpy(cmd, command);
    int argc = 0;
    char** argv = NULL;
    char* token;
    char* sep = " \t\n";
    for (token = strtok(cmd, sep); token;
        token = strtok(NULL, sep)) {
        argv = realloc(argv, (++argc) * sizeof(
            *argv));
        argv[argc - 1] = malloc((strlen(token) +
            1) * sizeof(*token));
        strcpy(argv[argc - 1], token);
    }
    argv = realloc(argv, (++argc) * sizeof(
        *argv));
    argv[argc - 1] = NULL;
    return argv;
}
void freeargv(char** argv) {
    for (int i = 0; argv[i]; ++i)
        free(argv[i]);
    free(argv);
}
int mysystem(const char* command) {
    char** argv = getargv(command);
    pid_t pid = vfork();
    if (pid == -1) {
        perror("vfork");
        freeargv(argv);
        return -1;
    }
    if (pid == 0)
        if (execvp(argv[0], argv) == -1) {
            perror("execvp");
            _exit(127);
        }
    int status;
    if (waitpid(pid, &status, 0) == -1) {
        perror("waitpid");
        freeargv(argv);
        return -1;
    }
    freeargv(argv);
    return status;
}
int main(void) {
    int status;
    if ((status = mysystem("ls -l")) == -1) {
        perror("system");
        return -1;
    }
    printf("退出码：%d\n", WEXITSTATUS(status));
    if ((status = mysystem("ps u")) == -1) {
        perror("system");
        return -1;
    }
    printf("退出码：%d\n", WEXITSTATUS(status));
    /*
    char** argv = getargv(
        "gcc -c hello.c -o hello.o");
    char** pp = argv;
    while (pp && *pp)
        printf("%s\n", *pp++);
    freeargv(argv);
    */
    return 0;
}   
```

## 十一、信号

### 11.1 基本概念

1)什么是信号？
                        事件(信号)
过程(进程)1---------v--------->
                               /   \
                             /事件\
过程(进程)2        /---v---\
                                /  \
                              /      \
过程(进程)3         /--- ---\
信号是提供异步事件处理机制的软件中断。这些异步事件可能来自硬件设备，也可能来自系统内核，甚至可能来自用户程序。进程之间可以相互发送信号，这使信号成为一种进程间通信(Inter-Process Communication, IPC)的基本手段。信号的异步特性不仅表现为它的产生是异步的，对它的处理同样也是异步的。程序设计者不可能也不需要精确地预见什么时候触发什么信号，也同样无法预见该信号究竟在什么时候会被处理。一切尽在内核操控下异步地发生。
2)什么是信号处理？
每一个信号都有其生命周期：
产生：信号被生成，并被发送至系统内核
未决：信号被内核缓存，而后被递送至目标进程
递送：内核已将信号发送至目标进程
           忽略 - 什么也不做。
           捕获 - 暂定当前的执行过程，转而调用一个事先写好
                     的信号处理函数，待该函数完成并返回后，再
                     继续之前被中断的过程。
           默认 - 既不忽略该信号，也不用自己定义处理方式，
                     而是按照系统默认的方式予以响应。
激励(信号)->响应(信号处理)
3)信号的名称和编号
信号名称：形如SIGXXX的字符串或宏定义，提高可读性。
信号编号：整数
通过kill -l命令查看当前系统所支持的全部信号名称及其编号。
1~31，31个不可靠信号，也叫非实时信号。
34~64， 31个可靠信号，也叫实时信号。(不会丢，会保留顺序)
共62个信号，注意没有32和33信号。
SIGHUP(1)，控制终端关闭，终止
SIGINT(2)，用户产生中断符(Ctrl+C)，终止
SIGQUIT(3)，用户产生退出符(Ctrl+\)，终止+转储 （kill -SIGQUIT -1  ,给所有进程都发信号）
SIGBUS(7)，硬件或内存对齐错误，终止+转储
SIGKILL(9)，不能被捕获和忽略，终止
SIGSEGV(11)，无效内存访问，终止+转储
SIGPIPE(13)，向读端已关闭的管道写入，终止
SIGALRM(14)，alarm函数设置的闹钟到期，终止
SIGTERM(15)，可被捕获和忽略，终止
SIGCHLD(17)，子进程终止，忽略 （太平间信号）
SIGIO(29)，异步I/O事件，终止
代码：loop.c

```c
#include <stdio.h>
#include <unistd.h>
int main(void) {
    printf("PID: %d\n", getpid());
    for (;;);
    return 0;
}
```

### 11.2 捕获信号

```c
#include <signal.h>
typedef void (*sighandler_t) (int);
                                    |
                             函数指针
   指向一个接受整型参数且无返回值的函数
设置针对特定信号的处理方式，即捕获特定的信号：
sighandler_t signal(int signum, sighandler_t handler);
成功返回原信号处理方式，失败返回SIG_ERR(sighandler_t类型的-1)。
signum - 信号编号
handler - 信号处理函数指针，也可以取以下值：
                 SIG_IGN - 忽略信号
                 SIG_DFL - 默认操作
...
// 定义信号处理函数
void sigint (int signum) {
    SIGINT(2)信号的处理代码
}
...
// 捕获SIGINT(2)信号
if (signal(SIGINT, sigint) == SIG_ERR) {
    perror("signal");
    return -1;
}
...
                                        SIGINT(2)
                                               v
SIGINT(2)/PID/sigint->系统内核
                                               v
                             目标进程中的sigint函数
signal(SIGINT, SIG_DFL); // 按默认方式处理
signal(SIGINT, SIG_IGN); // 忽略信号
```

当一个信号正在被处理的过程中，相同的信号再次产生，该信号会被阻塞，直到前一个信号处理完成，即从信号处理函数中返回，后一个被阻塞的信号才被递送，进而再次执行信号处理函数。当一个不可靠信号正在被处理的过程中，多个相同的信号再次产生，只有第一个信号会被阻塞，其它信号直接丢弃，如果是可靠信号，都会被阻塞，并按照产生的顺序依次被递送。
信号处理函数及被其调用的函数都有可能发生重入，由此可能引发无可预知的风险。
global = 0;
...
++global;
...
所有标准I/O函数都是不可重入函数。在信号处理的过程中要慎用。
A信号->A信号处理函数 \ 打印AAAAAA
                                                                            \>printf(调试信息);
B信号->B信号处理函数 / 打印BBBBBB
AAABBBBBBAAA
代码：signal.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
void sigint(int signum) {
    signal(SIGINT, SIG_DFL);
    printf("%d进程：收到%d信号！\n",
        getpid(), signum);
    signal(SIGINT, sigint);
}
void sigterm(int signum) {
    printf("%d进程：收到%d信号！\n",
        getpid(), signum);
    printf("妥当善后...\n");
    exit(0);
}
int main(void) {
    if (signal(/*2*/SIGINT, sigint) == SIG_ERR) {
        perror("signal");
        return -1;
    }
    if (signal(/*3*/SIGQUIT, SIG_IGN) ==
        SIG_ERR) {
        perror("signal");
        return -1;
    }
    if (signal(/*9*//*SIGKILL*//*15*/SIGTERM,
        /*SIG_IGN*/sigterm/*SIG_DFL*/)==SIG_ERR){
        perror("signal");
        return -1;
    }
    for (;;);
    return 0;
}
```

### 11.3 信号捕获流程

```c
                       中断
主控制流程---------       v       ----------->
                /                \
信号处理函数     /      ------>     \      用户空间
              /       \    /       \     ---------
             /         \  /         \    内核空间
内核处理流程    -----------> ---------->
           /  do_signal    system_call          \
    保护现场  handle_signal  sys_sigreturn         恢复现场
           \  setup_frame   restore_sigcontext  /
信号的本质是一个中断的处理过程，而非多线程的并发过程。
             线程安全的函数未必是可重入函数。
      ---------                      -------
         锁机制                        局部化
```

### 11.4 信号捕获的一次性问题

在某些非Linux操作系统上，存在信号捕获的一次性问题：
即使设置了对某个信号的捕获，只有设置后的第一个该信号被递送时，信号处理函数会被执行，以后再来相同的信号，均按默认方式处理。如果希望对信号的捕获具有持久性，可以在信号处理函数返回前再次设置对该信号的捕获。

### 11.5 太平间信号

通过SIGCHLD(17)信号高效地回收子进程僵尸。
高效：及时性，适时性。

**注意：**当子进程结束时，会向父进程递送17信号。17信号是不可靠信号，如果回收速度不够快，后面的信号会被丢弃，可以用一个信号回收多个僵尸进程来解决。

代码：sigchld.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <errno.h>
#include <unistd.h>
#include <sys/wait.h>
#include <signal.h>
void sigchld(int signum) {
    for (;;) {
        pid_t pid = waitpid(-1, NULL, WNOHANG);
        if (pid == -1) {
            if (errno != ECHILD) {
                perror("wait");
                exit(-1);
            }
            printf("父进程：所有子进程"
                "都已终止。\n");
            break;
        }
        if (!pid) {
            printf("父进程：暂时没有可回收的"
                "子进程。\n");
            break;
        }
        printf("父进程：%d子进程终止。\n", pid);
    }
    usleep(100000);
}
int main(void) {
    if (signal(SIGCHLD, sigchld) == SIG_ERR) {
        perror("signal");
        return -1;
    }
    for (int i = 0; i < 5; ++i) {
        pid_t pid = fork();
        if (pid == -1) {
            perror("fork");
            return -1;
        }
        if (pid == 0) {
            printf("子进程：我是%d进程。"
                "我要退出了。\n", getpid());
            return 0;
        }
    }
    getchar();
    return 0;
}
```

### 11.6 信号处理的继承与恢复

1)fork/vfork函数创建的子进程会继承父进程的信号处理方式，直到子进程调用exec函数创建新进程替代其自身为止。
2)exec函数创建的新进程会将原进程中被设置为捕获的信号还原为默认处理。在原进程中被忽略的信号于新进程中继续被忽略。
代码：fork.c

```c
#include <stdio.h>
#include <unistd.h>
#include <signal.h>
void sigint(int signum) {
    printf("%d进程：收到%d信号！\n",
        getpid(), signum);
}
int main(void) {
    if (signal(SIGINT, sigint) == SIG_ERR) {
        perror("signal");
        return -1;
    }
    if (signal(SIGQUIT, SIG_IGN) == SIG_ERR) {
        perror("signal");
        return -1;
    }
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        printf("%d进程：我是子进程，"
            "正在运行中...\n", getpid());
        for (;;);
        return 0;
    }
    sleep(1);
    printf("%d进程：我是父进程，马上要退出。\n",
        getpid());
    return 0;
}
```

### 11.7 发送信号

1)通过键盘向当前拥有控制终端的前台进程发送信号
Ctrl+C -> SIGINT(2)，默认终止进程
Ctrl+\ -> SIGQUIT(3)，默认终止进程且转储
Ctrl+Z -> SIGTSTP(20)，默认停止(挂起)进程
2)来自硬件或者内核的错误和异常引发的信号
SIGILL(4)，进程试图执行非法指令
SIGBUS(7)，硬件或总线对齐错误
SIGFPE(8)，浮点异常
SIGSEGV(11)，无效内存访问
SIGPIPE(13)，向无读端的管道写入
SIGSTKFLT(16)，浮点数协处理器栈错误
SIGXFSZ(25)，文件资源超限
SIGPWR(30)，断电
SIGSYS(31)，无效系统调用
3)通过kill命令发送信号
kill [-信号] PIDs
           |
缺省发送SIGTERM(15)信号
超级用户可以给任何进程发信号，普通用户只能给自己的进程发信号。

4)调用函数发送信号
向特定的进程(组)发送信号：

```c
int kill(pid_t pid, int signum);
成功(至少发出去一个信号)返回0，失败返回-1。
pid - 进程(组)标识，可取以下值：
<-1：向-pid进程组中的所有进程发送信号
-1: 向系统中的所有进程发送信号
0: 向调用进程同组的所有进程发送信号
>0: 向进程标识为pid的特定进程发送信号
signum - 信号编号，取0用于检查pid进程是否存在，如果不存在，kill函数会返回-1，且置errno为ESRCH。
```

代码：kill.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
#include <sys/wait.h>
#include <errno.h>
void sigint(int signum) {
    printf("%d进程：收到%d信号！\n",
        getpid(), signum);
    exit(0);
}
int isdead(pid_t pid) {
    if (kill(pid, 0) == -1) {
        if (errno != ESRCH) {
            perror("kill");
            exit(-1);
        }
        return 1;
    }
    return 0;
}
int main(void) {
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        printf("%d进程：我是子进程，"
            "正在运行中...\n", getpid());
        if (signal(SIGINT, sigint) == SIG_ERR) {
            perror("signal");
            return -1;
        }
        for (;;);
        return 0;
    }
    sleep(1);
    printf("%d进程：我是父进程，我要发信号...\n",
        getpid());
    if (kill(pid, SIGINT) == -1) {
        perror("kill");
        return -1;
    }
    printf("%d进程：%d进程%s终止。\n",
        getpid(), pid,
        isdead(pid) ? "已然" : "尚未");
    if ((pid = wait(NULL)) == -1) {
        perror("wait");
        return -1;
    }
    printf("%d进程：%d进程%s终止。\n",
        getpid(), pid,
        isdead(pid) ? "已然" : "尚未");
    return 0;
}
```

向调用进程自己发送信号：
int raise(int signum);
成功返回0，失败返回-1。
signum - 信号编号
raise(SIGINT);
kill(getpid(), SIGINT);
代码：raise.c
通过raise或kill向调用进程发送信号，如果该信号被捕获，则要等到信号处理函数返回后，这两个函数才会返回。

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
void sigint(int signum) {
    printf("%d进程：收到%d信号！\n",
        getpid(), signum);
    usleep(100000);
    /*
    exit(0);
    */
}
int main(void) {
    if (signal(SIGINT, sigint) == SIG_ERR) {
        perror("signal");
        return -1;
    }
    printf("%d进程：我要给自己发信号...\n",
        getpid());
    for (int i = 0; i < 5; ++i)
        /*
        if (raise(SIGINT) == -1) {
            perror("raise");
            return -1;
        }
        */
        if (kill(getpid(), SIGINT) == -1) {
            perror("kill");
            return -1;
        }
    /*
    for (;;)
        printf("%d进程：我还活着...\n",
            getpid());
    */
    for (;;);
    return 0;
}
```

### 11.8 暂停、睡眠和闹钟

暂停，即不受时间限制的睡眠：
int pause(void);
成功阻塞，失败返回-1。
该函数使调用进程进入无时限的睡眠状态，即不参与内核调度，直到有信号终止了调用进程或被捕获。如果有信号被调用进程捕获，当信号处理函数返回以后，pause函数才会返回，且返回值为-1，同时置errno为EINTR，表示阻塞的系统调用被信号中断。pause函数要么不返回，要么返回-1，不会返回0。
代码：pause.c

```c
#include <stdio.h>
#include <unistd.h>
#include <signal.h>
#include <sys/wait.h>
void sigint(int signum) {
    printf("%d进程：收到%d信号！\n",
        getpid(), signum);
}
int main(void) {
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        if (signal(SIGINT, sigint) == SIG_ERR) {
            perror("signal");
            return -1;
        }
        printf("%d进程：我是子进程，"
            "大睡无期...\n", getpid());
        pause();
        printf("%d进程：我醒了，即将退出。\n",
            getpid());
        return 0;
    }
    sleep(10);
    printf("%d进程：我是父进程，"
        "向子进程发送信号...\n", getpid());
    if (kill(pid, SIGINT) == -1) {
        perror("kill");
        return -1;
    }
    if ((pid = wait(NULL)) == -1) {
        perror("wait");
        return -1;
    }
    printf("%d进程：子进程已终止。\n", getpid());
    return 0;
}
```

受时间限制的睡眠：
unsigned int sleep(unsigned int seconds);
返回0或剩余秒数。
seconds - 以秒为单位的睡眠时限
该函数使调用进程睡眠seconds秒，除非有信号终止了调用进程或被其捕获。如果有信号被调用进程捕获，在信号函数返回以后，sleep函数才会返回，且返回值为剩余秒数，否则该函数返回0，表示睡眠充足。
代码：sleep.c

```c
#include <stdio.h>
#include <unistd.h>
#include <signal.h>
#include <sys/wait.h>
void sigint(int signum) {
    printf("%d进程：收到%d信号！\n",
        getpid(), signum);
}
int main(void) {
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        if (signal(SIGINT, sigint) == SIG_ERR) {
            perror("signal");
            return -1;
        }
        printf("%d进程：我是子进程，"
            "睡眠10秒...\n", getpid());
        unsigned int left = sleep(10);
        printf("%d进程：我醒了，"
            "还剩%u秒没睡，即将退出。\n",
            getpid(), left);
        return 0;
    }
    sleep(15);
    printf("%d进程：我是父进程，"
        "向子进程发送信号...\n", getpid());
    if (kill(pid, SIGINT) == -1) {
        perror("kill");
        return -1;
    }
    if ((pid = wait(NULL)) == -1) {
        perror("wait");
        return -1;
    }
    printf("%d进程：子进程已终止。\n", getpid());
    return 0;
}
```

int usleep(useconds_t usec);
睡够了返回0，睡不够返回-1，同时置errno为EINTR。
usec - 为微秒为单位的睡眠时限
1微秒=10^-6秒
Intel CPU：50~55毫秒
设置闹钟
unsigned int alarm(unsigned int seconds);
返回0或先前闹钟的剩余时间。
seconds - 以秒为单位的闹钟时间
alarm函数使系统内核在该函数被调用以后seconds秒的时候，向调用进程发送SIGALRM(14)信号。若在调用该函数前已设过闹钟且尚未到期，则该函数会重设闹钟，并返回先前所设闹钟的剩余秒数，否则返回0。若seconds参数取0，则取消之前设置过且未到期的闹钟。
代码：alarm.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
void sigalrm(int signum) {
    printf("%d进程：收到%d信号！\n",
        getpid(), signum);
    exit(0);
}
int main(void) {
    if (signal(SIGALRM, sigalrm) == SIG_ERR) {
        perror("signal");
        return -1;
    }
    alarm(10);
    printf("定时10秒。\n");
    printf("睡眠3秒...\n");
    sleep(3);
    /*
    unsigned int remain = alarm(5);
    printf("定时5秒，剩余%u秒。\n", remain);
    */
    unsigned int remain = alarm(0);
    printf("取消定时，剩余%u秒。\n", remain);
    for (;;);
    return 0;
}
```

通过alarm函数所设置的定时只是一次性的，即在定时到期时发行一次SIGALRM(14)信号，此后不会再发送该信号。如果希望获得周期性的定时效果，可以在SIGALRM(14)信号的处理函数中继续调用alarm函数，完成下一个定时的设置。
代码：clock.c

```c
#include <stdio.h>
#include <time.h>
#include <unistd.h>
#include <signal.h>
void sigalrm(int signum) {
    time_t t = time(NULL);
    struct tm* lt = localtime(&t);
    printf("\r%02d:%02d:%02d", lt->tm_hour,
        lt->tm_min, lt->tm_sec);
    alarm(1);
}
int main(void) {
    setbuf(stdout, NULL);
    if (signal(SIGALRM, sigalrm) == SIG_ERR) {
        perror("signal");
        return -1;
    }
    sigalrm(SIGALRM);
    for (;;);
    return 0;
}
```

### 11.9 信号集

```c
#include <signal.h>
typedef __sigset_t sigset_t;
#include <sigset.h>
typedef struct {
    unsigned long int __val[_SIGSET_NWORDS];
                            32 x 32 = 1024位
} __sigset_t;
#define _SIGSET_NWORDS \
    (1024 / (8 * sizeof(unsigned long int)))
|<-1024位->|
- - - - - -  - -  -
            4 3 2 1
void foo(int a[10]) {}
void foo(int a[]) {}
void foo(int* a) {}
struct Student { ... };
void bar(struct Student s) { ... }
int main(void) {
    int a[10] = { ... };
    foo(a);
    ...
    struct Student s = { int a[10]; };
    bar(s);
}
```

填满信号集，即将信号集的全部信号位置1
int sigfillset(sigset_t* sigset);
清空信号集，即将信号集的全部信号位置0
int sigemptyset(sigset_t* sigset);
加入信号，即将信号集中的特定信号位置1
int sigaddset(sigset_t* sigset, int signum);
删除信号，即将信号集中的特定信号位置0
int sigdelset(sigset_t* sigset, int signum);
成功返回0，失败返回-1。
检查信号，即判断信号集中的特定信号位是否为1
int sigismember(sigset_t* sigset, int signum);
有则返回1，无则返回0，失败返回-1。
代码：sigset.c

```c
#include <stdio.h>
#include <signal.h>
void printb(char byte) {
    for (int i = 0; i < 8; ++i)
        printf("%c",
            byte & 1 << 7 - i ? '1' : '0');
    printf(" ");
}
void printm(void* buff, size_t size) {
    for (int i = 0; i < size; ++i) {
        printb(((char*)buff)[size-i-1]);
        if ((i + 1) % 4 == 0 || i == size - 1)
            printf("\n");
    }
}
int main(void) {
    sigset_t sigset;
    printf("满信号集：\n");
    sigfillset(&sigset);
    printm(&sigset, sizeof(sigset));
    printf("空信号集：\n");
    sigemptyset(&sigset);
    printm(&sigset, sizeof(sigset));
    printf("加入SIGINT(2)信号：\n");
    sigaddset(&sigset, SIGINT);
    printm(&sigset, sizeof(sigset));
    printf("加入SIGQUIT(3)信号：\n");
    sigaddset(&sigset, SIGQUIT);
    printm(&sigset, sizeof(sigset));
    printf("删除SIGQUIT(3)信号：\n");
    sigdelset(&sigset, SIGQUIT);
    printm(&sigset, sizeof(sigset));
    printf("%d\n", sigismember(&sigset,SIGINT));
    printf("%d\n", sigismember(&sigset,SIGQUIT));
    return 0;
}
```

### 11.10 信号屏蔽

1)递送、未决和掩码
当信号产生时，系统内核在目标进程的进程表项中，以信号位置1的方式，存储该信号，这个过程就叫做递送。信号从产生到完成递送之间存在一定的时间间隔，处于该间隔状态的信号就是属于未决信号。每个进程都已一个信号掩码，它实际上是一个信号集，位于该信号集中的信号一旦产生，并不会被递送给相应的进程，而是会被阻塞于未决状态。
当进程正在执行类似更新数据库、设置硬件状态等敏感任务时，可能不希望被某些信号中断。这是可以通过信号掩码暂时屏蔽而非忽略这些信号，使其一旦产生即被阻塞于未决状态，待特定任务完成以后，在恢复对这些信号的处理。

2)设置信号掩码

```c
int sigprocmask(int how, const sigset_t* sigset
   sigset_t* oldset);
成功返回0，失败返回-1。
how - 信号掩码的修改方式，可取以下值：
SIG_BLOCK - 将sigset中的信号加入当前掩码
SIG_UNBLOCK - 从当前掩码中删除sigset中的信号
SIG_SETMASK - 将sigset设置为当前掩码
sigset - 信号集，取NULL则忽略参数
oldset - 原信号掩码，取NULL则忽略此参数
```

3)获取未决信号
int sigpending(sigset_t* sigset);
成功返回0，失败返回-1。
sigset - 输出未决信号集

4)不可靠信号最多被信号掩码屏蔽一次，在屏蔽期间再有更多的相同信号，一律被丢弃。可靠信号会全部被保留下来，且按照发送的顺序排成队列。
代码：sigmask.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
void sighand(int signum) {
    printf("%d进程：收到%d信号！\n",
        getpid(), signum);
}
void update(void) {
    for (int i = 0; i < 3; ++i) {
        printf("%d进程：更新第%d条记录。\n",
            getpid(), i + 1);
        sleep(1);
    }
}
int main(void) {
    if (signal(SIGINT, sighand) == SIG_ERR) {
        perror("signal");
        return -1;
    }
    if (signal(50, sighand) == SIG_ERR) {
        perror("signal");
        return -1;
    }
    printf("%d进程：屏蔽%d信号和%d信号。\n",
        getpid(), SIGINT, 50);
    sigset_t newset;
    sigemptyset(&newset);
    sigaddset(&newset, SIGINT);
    sigaddset(&newset, 50);
    sigset_t oldset;
    if (sigprocmask(SIG_SETMASK, &newset,
        &oldset) == -1) {
        perror("sigprocmask");
        return -1;
    }
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        pid_t ppid = getppid();
        for (int i = 0; i < 3; ++i) {
            printf("%d进程：向%d进程发送"
                "%d信号和%d信号...\n", getpid(),
                ppid, SIGINT, 50);
            kill(ppid, SIGINT);
            kill(ppid, 50);
        }
        return 0;
    }
    update();
    if (sigprocmask(SIG_SETMASK, &oldset,
        NULL) == -1) {
        perror("sigprocmask");
        return -1;
    }
    getchar();
    return 0;
}
```

### 11.11 现代版本的信号处理与发送

经典版本的信号处理与发送：signal/kill(raise)
现代版本的信号处理与发送：sigaction/sigqueue
int sigaction(int signum, const struct sigaction* sigact,
    struct sigaction* oldact);
成功返回0，失败返回-1。
signum - 信号编号
sigact - 信号行为
oldact - 输出原来的信号行为，可置NULL
当signum信号被递送时，按sigact结构所描述的行为响应之。若oldact参数非NULL，则通过该参数输出原来的响应行为。

```c
struct sigaction {
    // 经典版本的信号处理函数指针
    void (*sa_handler)(int);
    // 现代版本的信号处理函数指针
    void (*sa_sigaction)(int, siginfo_t*, void*);
    // 信号处理期间的附加掩码集
    sigset_t sa_mask;
    // 信号处理标志
    int sa_flags;
    // 预留项，目前置NULL
    void (*sa_restorer)(void);
};
现代版本的信号处理函数：
                 信号编号         信号信息        保留未用
                   |               |              |
void sigint(int signum, siginfo_t* si, void* reserved) {
    ...
}
typedef struct siginfo {
    // 发送信号进程的PID  \
    pid_t si_pid;       |
    // 信号附加数据       | 18个字段
    sigval_t si_value;  |
    ...                 /
}  siginfo_t;
typedef union sigval {
    int      sival_int; // 用整型作为信号附加数据
    void* sival_ptr; // 用任意类型的指针作为信号附加数据
}  sigval_t;
```

1)增减信号掩码
缺省情况下，在信号处理函数的执行过程中，会自动屏蔽这个正在被处理的信号，而对于其它信号则不会屏蔽。通过sa_mask字段可以人为指定，在信号处理函数执行期间，除正在被处理的这个信号以外，还想屏蔽哪些信号，并在信号处理函数返回后，自动解除对它们的屏蔽。另一方面，还可以通过为sa_flags字段设置SA_NOMASK/SA_NODEFER标志位，告诉系统内核在信号处理函数执行期间，不要屏蔽这个正在被处理的信号。
2)选择信号处理函数的风格
如果sa_flags字段中没有SA_SIGINFO标志位，则sa_handler字段有效，即使用经典版本的信号处理函数。相反，如果sa_flags字段包含SA_SIGINFO标志位，则sa_sigaction字段有效，即使用现代版本的信号处理函数。
3)一次性信号处理
如果sa_flags字段中包含SA_ONESHOT/SA_RESETHAND标志位，那么对所捕获信号的处理就是一次性的，即在执行完一次信号处理函数后，即恢复为按默认方式处理。
代码：sigact.c

```c
#include <stdio.h>
#include <unistd.h>
#include <signal.h>
void sigint1(int signum) {
    printf("收到%d信号！睡眠中...\n", signum);
    sleep(5);
    printf("睡醒了。\n");
}
void sigint2(int signum, siginfo_t* si,
    void* pv) {
    printf("收到发自%d进程的%d信号！\n",
        si->si_pid, signum);
}
int main(void) {
    struct sigaction sa = {};
    /*
    printf("经典版本信号处理函数执行期间只屏蔽"
        "SIGINT信号，不屏蔽SIGQUIT信号。\n");
    sa.sa_handler = sigint1;
    *//*
    printf("经典版本信号处理函数执行期间既屏蔽"
        "SIGINT信号，也屏蔽SIGQUIT信号。\n");
    sa.sa_handler = sigint1;
    sigaddset(&sa.sa_mask, SIGQUIT);
    *//*
    printf("经典版本信号处理函数执行期间不屏蔽"
        "SIGINT信号，也不屏蔽SIGQUIT信号。\n");
    sa.sa_handler = sigint1;
    sa.sa_flags = SA_NOMASK;
    *//*
    printf("经典版本信号处理函数执行期间不屏蔽"
        "SIGINT信号，只屏蔽SIGQUIT信号。\n");
    sa.sa_handler = sigint1;
    sigaddset(&sa.sa_mask, SIGQUIT);
    sa.sa_flags = SA_NOMASK;
    *//*
    printf("现代版本信号处理函数。\n");
    sa.sa_sigaction = sigint2;
    sa.sa_flags = SA_SIGINFO;
    */
    printf("一次性信号处理。\n");
    sa.sa_handler = sigint1;
    sa.sa_flags = SA_ONESHOT;
    if (sigaction(SIGINT, &sa, NULL) == -1) {
        perror("sigaction");
        return -1;
    }
    for (;;) pause();
    return 0;
}
```

4)系统调用中断重启
诸如pause、sleep、usleep、read、read等系统调用的阻塞过程会被信号打断，即在针对某个信号的处理函数返回后，这些系统调用也会从阻塞中返回。如果不希望这类系统调用被所捕获的信号打断，即在针对该信号的处理函数返回后，能够自动重启阻塞过程，而不要返回，可以为sa_flags字段添加SA_RESTART标志位。
代码：restart.c

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <signal.h>
#include <errno.h>
void sigint(int signum) {
    printf("收到%d信号！\n", signum);
}
int main(void) {
    struct sigaction sa = {};
    sa.sa_handler = sigint;
    sa.sa_flags = SA_RESTART;
    if (sigaction(SIGINT, &sa, NULL) == -1) {
        perror("sigaction");
        return -1;
    }
    ssize_t len;
    char buf[256] = {};
//again:
    if ((len = read(STDIN_FILENO, buf,
        sizeof(buf))) == -1) {
        //if (errno == EINTR)
            //goto again;
        perror("read");
        return -1;
    }
    if (write(STDOUT_FILENO, buf, len) == -1) {
        perror("write");
        return -1;
    }
    return 0;
}
```

int sigqueue(pid_t pid, int signum,
    const union sigval value);
成功返回0，失败返回-1。
pid - 接收信号进程的PID
signum - 信号编号
value - 信号附加数据 -> siginfo::si_value
代码：sigque.c

```c
#include <stdio.h>
#include <unistd.h>
#include <signal.h>
#include <sys/wait.h>
void siginfo(int signum, siginfo_t* si,
    void* pv) {
    printf("%d进程：收到来自%d进程的%d信号"
        "(附加值=%d)！\n", getpid(), si->si_pid,
        signum, si->si_value.sival_int);
    usleep(100);
}
int main(void) {
    int signum = /*50*/SIGINT;
    struct sigaction sa = {};
    sa.sa_sigaction = siginfo;
    sa.sa_flags = SA_SIGINFO | SA_RESTART;
    if (sigaction(signum, &sa, NULL) == -1) {
        perror("sigaction");
        return -1;
    }
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        pid_t ppid = getppid();
        for (int i = 0; i < 10; ++i) {
            sigval_t sv;
            sv.sival_int = i;
            printf("%d进程：向%d进程发送%d信号"
                "(附加值=%d)...\n", getpid(),
                ppid, signum, sv.sival_int);
            if (sigqueue(ppid, signum,
                sv) == -1) {
                perror("sigqueue");
                return -1;
            }
        }
        printf("%d进程：我即将退出。\n",
            getpid());
        return 0;
    }
    if ((pid = wait(NULL)) == -1) {
        perror("wait");
        return -1;
    }
    printf("%d进程：%d进程已退出。\n",
        getpid(), pid);
    return 0;
}
```

利用信号附加数据实现简单的进程间通信。
代码：send.c、recv.c

```c
send.c

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <signal.h>
int main(int argc, char* argv[]) {
    if (argc < 2) {
        fprintf(stderr, "用法：%s <PID>\n",
            argv[0]);
        return -1;
    }
    char buf[1024];
    for (;;) {
        printf("> ");
        fgets(buf, sizeof(buf) / sizeof(
            buf[0]), stdin);
        if (!strcmp(buf, "!\n"))
            break;
        /*
        sigval_t sv = {};
        sv.sival_ptr = buf;
        sigqueue(atoi(argv[1]), 50, sv);
        */
        for (int i = 0; buf[i]; ++i) {
            sigval_t sv = {};
            sv.sival_int = buf[i];
            if (sigqueue(atoi(argv[1]),
                /*50*/SIGINT, sv) == -1) {
                perror("sigqueue");
                return -1;
            }
            usleep(10000);
        }
    }
    return 0;
}

recv.c

#include <stdio.h>
#include <unistd.h>
#include <signal.h>
void sigact(int signum, siginfo_t* si, void* pv){
    printf("%c", si->si_value.sival_int);
}
int main(void) {
    printf("PID: %d\n", getpid());
    setbuf(stdout, NULL);
    struct sigaction sa = {};
    sa.sa_sigaction = sigact;
    sa.sa_flags = SA_SIGINFO | SA_RESTART;
    if (sigaction(/*50*/SIGINT, &sa,
        NULL) == -1) {
        perror("sigaction");
        return -1;
    }
    getchar();
    return 0;
}
```

### 11.12 定时器

执行时间=用户时间+内核时间+睡眠时间
      |                |                |               |
直观感受 消耗在用户 消耗在内核 消耗在等待I/O、睡
墙钟时间   态的时间    态的时间   眠等不被调度的时间
      |                |                |
  真实计      虚拟计           /
   时器          时器           /
                        |_ _ _ _ /
                              |
                     实用计时器
利用真实计时器定时，到期信号：SIGALRM(14)
利用虚拟计时器定时，到期信号：SIGVTALRM(26)
利用实用计时器定时，到期信号：SIGPROF(27)
基于每钟计时器的定时都有两个参数：
初始间隔 - 从开始定时到发出第一个到期信号的时间间隔。
重复间隔 - 每相邻两个到期信号的时间间隔。
|--------------------------+---------------+-------------+--->
|      <-初始间隔->    |<重复间隔>|
                            

```c
设置、开启和关闭定时器：
int setitimer(
    int which,
    const struct itimerval* new_value,
    struct itimerval* old_value);
成功返回0，失败返回-1。
which - 指定设置哪个定时器，可取以下值：
ITIMER_REAL: 基于真实计时器的定时
ITIMER_VIRTUAL: 基于虚拟计时器的定时
ITIMER_PROF: 基于实用计时器的定时
new_value - 新设置值
old_value - 输出原设置值，可置NULL
struct itimerval {
    // 重复间隔，取0表示只发一个信号，不重复，没有周期性
    struct timeval it_interval;
    // 初始间隔，取0表示停止定时器，不再发信号
    struct timeval it_value;
};
struct timeval {
    long tv_sec; // 秒
    long tv_usec; // 微秒
};
例如：5秒以后开始发SIGALRM(14)信号，以后每隔3毫秒再发一次该信号。
struct itimerval it;
it.it_value.tv_sec = 5;
it.it_value.tv_usec = 0;
it.it_interval.tv_sec = 0;
it.it_interval.tv_usec = 3000;
setitimer(ITIMER_REAL, &it, NULL); // 开
...
it.it_value.tv_sec = 0;
setitimer(ITIMER_REAL, &it, NULL); // 关
如果希望立即启动定时器，初始间隔至少是1微秒。
获取当前定时器设置
int getitimer(int which, struct itimerval* curr_value);
成功返回0，失败返回-1。
which - 指定设置哪个定时器，可取以下值：
ITIMER_REAL: 基于真实计时器的定时
ITIMER_VIRTUAL: 基于虚拟计时器的定时
ITIMER_PROF: 基于实用计时器的定时
curr_value - 当前设置值
```

代码：timer.c

```c
#include <stdio.h>
#include <unistd.h>
#include <signal.h>
#include <sys/time.h>
int h = 0, m = 0, s = 0, ms = 0;
void sigalrm(int signum) {
    printf("\r%02d:%02d:%02d.%03d", h, m, s, ms);
    if (++ms == 1000) {
        ms = 0;
        if (++s == 60) {
            s = 0;
            if (++m == 60) {
                m = 0;
                ++h;
            }
        }
    }
}
void sigint(int signum) {
    static int run = 1;
    struct itimerval it = {};
    if (!run) {
        printf("\n");
        h = m = s = ms = 0;
        it.it_value.tv_usec = 1;
        it.it_interval.tv_usec = 1000;
    }
    setitimer(ITIMER_REAL, &it, NULL);
    run ^= 1;
}
int main(void) {
    setbuf(stdout, NULL);
    if (signal(SIGALRM, sigalrm) == SIG_ERR) {
        perror("signal");
        return -1;
    }
    if (signal(SIGINT, sigint) == SIG_ERR) {
        perror("sigint");
        return -1;
    }
    struct itimerval it = {{0, 1000}, {5, 0}};
    if (setitimer(ITIMER_REAL, &it, NULL) == -1){
        perror("setitimer");
        return -1;
    }
    if (getitimer(ITIMER_REAL, &it) == -1) {
        perror("getitimer");
        return -1;
    }
    printf("初始间隔：%ld秒%ld微秒\n",
        it.it_value.tv_sec,
        it.it_value.tv_usec);
    printf("重复间隔：%ld秒%ld微秒\n",
        it.it_interval.tv_sec,
        it.it_interval.tv_usec);
    for (;;) pause();
    return 0;
}
```

## 十二、进程间通信

### 12.1 什么是进程间的通信

Unix/Linux系统中每个进程都拥有独立的4G字节大小的虚拟内存空间。其中高地址的1G字节被映射到相同的物理内存区域，用于保存内核代码和数据。低地址的3G字节作为保存用户代码和数据的用户空间，被映射到彼此不同物理内存。因此同一个虚拟内存地址，在不同的进程中，会被映射到不同的物理内存区域，在多个进程之间以交换虚拟内存地址的方式交换数据是不可能的。鉴于进程之间天然的内存壁垒，为了能够在不同进程之间高效地交换数据，需要有一种专门的机制，这就是所谓的进程间通信(Inter-Process Communication, IPC)。

### 12.2 简单的进程间通信

```c
1)命令行参数
进程1组织命令行参数->execl->进程2处理命令行参数
进程1：username，password
execl(..., "login", username, password, NULL)
                       |           |
进程2：               argv[1]    argv[2]
2)环境变量
进程1组织环境变量->execle->进程2处理环境变量
进程1：username，password
sprintf(envp[0], "USERNAME=%s", username);
sprintf(envp[1], "PASSWORD=%s", password);
execle(..., envp);
进程2：envp[0]->USERNAME=minwei
             envp[1]->PASSWORD=tarena
3)wait/waitpid
进程1->fork/vfork+exec->进程2
wait/waitpid(..., &status); main->return ...;
                                     ^       exit/_exit/_Exit(...)
                                      |______________________|
4)内存映射文件
进程1[虚拟内存]-->文件区域<--[虚拟内存]进程2
         ^                    ^
         |____________________|
5)信号
进程1----信号+附加数据--->进程2
```

### 12.3 传统的进程间通信

1)有名管道
进程1<-管道文件->进程2
                    |
有i节点没有数据块，内存文件
全双工通信
2)无名管道
           / -无名管道-> \
父进程                         子进程
           \ <-无名管道- / 
半双工通信

### 12.4 XSI进程间通信(SVR4)

1)消息队列：进程1---消息3|消息2|消息1-->进程2
2)共享内存：进程1[虚拟内存]->物理内存<-[虚拟内存]进程2
3)信号量集：多个进程竞争有限的资源

**tips:**消息队列的好处是具有同步性，缺点是比较慢，因为消息队列要从应用拷贝到内核，在从内核拷贝到应用。

### 12.5 套接字进程间通信(BSD)

进程1<-本地套接字文件->进程2
                       |
   有i节点没有数据块，内存文件
以一种统一的编程模式和接口库，处理网络和本机通信。

### 12.6 有名管道

创建有名管道文件
#include <sys/stat.h>
int mkfifo(const char* pathname, model_t mode);
成功返回0，失败返回-1。
pathname - 文件路径
mode - 权限模式
打开、关闭、读取和写入有名管道文件的方法与读写普通文件无异：open/read/write/close。

**注意：**进程运行中关闭终端，并不会删除建立的管道文件

代码：wfifo.c、rfifo.c

```c
wfifo.c

#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <fcntl.h>
#include <sys/stat.h>
#define FIFO_FILE "myfifo"
int main(void) {
    printf("创建管道...\n");
    if (mkfifo(FIFO_FILE, 0666) == -1) {
        perror("mkfifo");
        return -1;
    }
    printf("打开管道...\n");
    int fd = open(FIFO_FILE, O_WRONLY);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    printf("发送数据...\n");
    for (;;) {
        printf("> ");
        char buf[1024];
        fgets(buf, sizeof(buf) / sizeof(buf[0]),
            stdin);
        if (!strcmp(buf, "!\n"))
            break;
        if (write(fd, buf, strlen(buf) * sizeof(
            buf[0])) == -1) {
            perror("write");
            return -1;
        }
    }
    printf("关闭管道...\n");
    if (close(fd) == -1) {
        perror("close");
        return -1;
    }
    printf("删除管道...\n");
    if (unlink(FIFO_FILE) == -1) {
        perror("unlink");
        return -1;
    }
    printf("完成！\n");
    return 0;
}

rfifo.c

#include <stdio.h>
#include <unistd.h>
#include <fcntl.h>
#define FIFO_FILE "myfifo"
int main(void) {
    printf("打开管道...\n");
    int fd = open(FIFO_FILE, O_RDONLY);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    printf("接收数据...\n");
    for (;;) {
        char buf[1024] = {};
        ssize_t rb = read(fd, buf, sizeof(buf) -
            sizeof(buf[0]));
        if (rb == -1) {
            perror("read");
            return -1;
        }
        // 写端已关闭且管道无数据
        if (!rb)
            break;
        printf("< %s", buf);
    }
    printf("关闭管道...\n");
    if (close(fd) == -1) {
        perror("close");
        return -1;
    }
    printf("完成！\n");
    return 0;
}
```

**编程模型**
进程A                                  进程B
创建管道     mkfifo
打开管道     open              打开管道
读写管道     read/write    读写管道
关闭管道     close              关闭管道
删除管道     unlink

### 12.7 无名管道

#include <unistd.h>
int pipe(int pipefd[2]);
成功返回0，失败返回-1。
pipefd - 输出两个文件描述符：
               pipefd[0]表示管道的读端
               pipefd[1]表示管道的写端
1)父进程调用pipe函数在系统内核中创建无名管道对象，同时得到与该对象相关联的两个文件描述符，一个用于读取，另一个用于写入；
2)父进程调用fork函数，创建子进程，子进程复制父进程的文件描述符表，因此子进程也同样拥有可用于读写管道对象的两个文件描述符。
3)负责写数据进程关闭管道读端，即pipefd[0]，而负责读数据的继承关闭管道的写端，即pipefe[1]；
4)父子进程通过各自持有的文件描述符，分别向管道写入和读取数据，待完成通信后再各自关闭所持有的文件描述符，内核中的无关管道对象即被释放。
代码：pipe.c

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <sys/wait.h>
int main(void) {
    printf("父进程：创建管道...\n");
    int pipefd[2];
    if (pipe(pipefd) == -1) {
        perror("pipe");
        return -1;
    }
    printf("父进程：创建子进程...\n");
    pid_t pid = fork();
    if (pid == -1) {
        perror("fork");
        return -1;
    }
    if (pid == 0) {
        printf("子进程：关闭写端...\n");
        close(pipefd[1]);
        printf("子进程：接收数据...\n");
        for(;;) {
            char buf[2014] = {};
            ssize_t rb = read(pipefd[0], buf,
                sizeof(buf) - sizeof(buf[0]));
            if (rb == -1) {
                perror("read");
                return -1;
            }
            if (!rb)
                break;
            fputs(buf, stdout);
        }
        printf("子进程：关闭读端...\n");
        close(pipefd[0]);
        printf("子进程：完成！\n");
        return 0;
    }
    printf("父进程：关闭读端...\n");
    close(pipefd[0]);
    printf("父进程：发送数据...\n");
    for (;;) {
        char buf[1024];
        fgets(buf, sizeof(buf) / sizeof(buf[0]),
            stdin);
        if (!strcmp(buf, "!\n"))
            break;
        if (write(pipefd[1], buf, strlen(buf) *
            sizeof(buf[0])) == -1) {
            perror("write");
            return -1;
        }
    }
    printf("父进程：关闭写端...\n");
    close(pipefd[1]);
    if (wait(NULL) == -1) {
        perror("wait");
        return -1;
    }
    printf("父进程：完成！\n");
    return 0;
}
```

### 12.8 基于管道通信的特殊情况

1)从写端已被关闭的管道中读取
只要管道缓冲区中还有数据，依然可被正常读取，一直读到缓冲区空，这时read函数会返回0（既不是返回-1也不是阻塞），如同读到文件尾。
2)向读端已被关闭的管道中写入
会直接触发SIGPIPE(13)信号。该信号的默认操作是终止执行写入动作的进程。但如果执行写入动作的进程已经事先将SIGPIPE(13)信号设置为忽略或捕获，这时虽然进程不会因为写入无读端的管道而被终止，但write函数会返回-1，并置errno为EPIPE。
3)在/usr/include/linux/limits.h头文件中定义的PIPE_BUF宏(4096)表示管道写缓冲区的大小。如果写管道时发现缓冲区中的空闲空间不足以容纳此次write调用所要写入的字节数，则write函数会阻塞，直到缓冲区中的空闲空间变得足够大为止。如果同时有多个进程向同一个管道写入数据，而每次调用write函数写入的字节数都不大于BUF_SIZE，则这些write操作不会互相穿插(原子化，atomic)，反之单次写入的字节数超过了BUF_SIZE，则它们的write操作可能会相互穿插。读取一个缓冲区为空的管道，只要其写端没有被关闭，读操作就会阻塞，除非该读文件描述符被设置为非阻塞(O_NONBLOCK)，此时会立即返回失败，并置errno为EAGAIN。

### 12.9 管道符号的原理

```c
命令1    |    命令2    |    命令3 ...
   |           |             |
输出>管道>输入
           输出>管道>输入
                            输出>...
A | B
Shell进程：
int pipefd[2];
pipe(pipefd); // 创建无名管道
vfork(); // 产生一个子进程1
    子进程1:
    close(pipefd[0]); // 关闭无名管道的读端
    dup2(pipefd[1], STDOUT_FILENO); // 写端=标准输出
    exec(A) // 创建A进程，继承了原进程的文件描述符表
                  // 在A进程中，写端=标准输出
                  // printf/puts将数据写入无名管道的写端
vfork(); // 产生一个子进程2
    子进程2:
    close(pipefd[1]); // 关闭无名管道的写端
    dup2(pipefd[0], STDIN_FILENO); // 读端=标准输入
    exec(B) // 创建B进程，继承了原进程的文件描述符表
                  // 在B进程集中，读端=标准输入
                  // scanf/gets从无名管道的读端读取数据
A和B就成为协作进程，A写入数据进管道，B从管道中读取A写入的数据。
```

代码：output.c、intput.c、shell.c

```c
output.c

#include <stdio.h>
int main(void) {
    int x = 123, y = 456;
    printf("%d %d\n", x, y);
    return 0;
}

input.c
    
#include <stdio.h>
int main(void) {
    int x, y;
    scanf("%d%d", &x, &y);
    printf("%d+%d=%d\n", x, y, x + y);
    return 0;
}

shell.c

#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>
#include <errno.h>
int main(void) {
    // 创建无名管道
    // 无名管道读端：pipefd[0]
    // 无名管道写端：pipefd[1]
    int pipefd[2];
    if (pipe(pipefd) == -1) {
        perror("pipe");
        return 0;
    }
    // 创建子进程，执行输出进程
    pid_t pid = vfork();
    if (pid == -1) {
        perror("vfork");
        return -1;
    }
    if (pid == 0) {
        // 关闭无名管道读端
        close(pipefd[0]);
        // 复制无名管道写端到标准输出
        if (dup2(pipefd[1],
            STDOUT_FILENO) == -1) {
            perror("dup2");
            _exit(-1);
        }
        // 创建输出进程
        // 该进程所有向标准输出的写操作
        // 实际都是在写入无名管道的写端
        if (execl("./output", "output",
            NULL) == -1) {
            perror("execl");
            _exit(-1);
        }
    }
    // 创建子进程，执行输入进程
    if ((pid = vfork()) == -1) {
        perror("vfork");
        return -1;
    }
    if (pid == 0) {
        // 关闭无名管道写端
        close(pipefd[1]);
        // 复制无名管道读端到标准输入
        if (dup2(pipefd[0],
            STDIN_FILENO) == -1) {
            perror("dup2");
            _exit(-1);
        }
        // 创建输入进程
        // 该进程所有从标准输入的读操作
        // 实际都是在读取无名管道的读端
        if (execl("./input", "input",
            NULL) == -1) {
            perror("execl");
            _exit(-1);
        }
    }
    close(pipefd[0]);
    close(pipefd[1]);
    for (;;)
        if (wait(NULL) == -1) {
            if (errno != ECHILD) {
                perror("wait");
                return -1;
            }
            break;
        }
    return 0;
}
```

### 12.10 XSI的IPC对象

1)IPC对象的标识符(ID)和键(KEY)

```c
IPC对象在系统内核中的唯一名称用键(KEY)表示。不同的进程可以通过键来引用该IPC对象。一旦进程获得了该IPC对象，即通过其标识(ID)来称谓该对象。  
     IPC对象(键)        内核空间
--- / ---| ---\ ---------------
   /键   |键    \键     用户空间
   进程1  进程2  进程3
  ID1   ID2    ID3
#include <sys/ipc.h>
key_t ftok(const char* pathname, int proj_id);
成功返回IPC对象的键，失败返回-1。
pathname - 一个真实存在的路径，使用该路径的i节点号
proj_id - 项目ID，仅低8为有效，-128~127/0~255
相同项目使用相同的pathname和proj_id，保证key的一致性
不同项目使用不同的pathname或proj_id，避免key发生冲突
```

2)IPC对象的编程接口

```c
A.创建或获取IPC对象(没有新建就获取)
// 共享内存
int shmget(key_t key, size_t size, int shmflg);
// 消息队列
int msgget(key_t key, int msgflg);
// 信号量集
int semget(key_t key, int nsems, int semflg);
B.控制或销毁IPC对象
// 共享内存
int shmctl(int shmid, int cmd, struct shmid_ds* buf);
// 消息队列
int msgctl(int msqid, int cmd, struct msqid_ds* buf);
// 信号量集
int semctl(int semid, int semnum, int cmd,
    union semun arg);
cmd - 控制命令，可取以下值：
IPC_STAT: 获取IPC对象的属性
IPC_SET: 设置IPC对象的属性
IPC_RMID: 删除IPC对象
C.IPC对象的权限结构
struct ipc_perm {
    key_t                   __key;   // 键
    uid_t                   uid;     // 拥有者用户
    gid_t                   gid;     // 拥有者组
    uid_t                   cuid;    // 创建者用户
    gid_t                   cgid;    // 创建者组
    unsigned short mode; //  权限
    unsigned short __seq;  // 序号
};
其中只有uid、gid和model三个字段可以在创建完成以后被修改。
```

3)共享内存

```c
两个或者更多进程，共享一块由系统内核负责维护的物理内存，其地址空间通常被映射到每个进程虚拟内存堆和栈之间的不同区域。
共享内存的属性结构：
struct shmid_ds {
    struct ipc_perm shm_prem;  // 权限结构
    size_t                   shm_segsz;  // 字节数
    time_t                  shm_atime;  // 最后加载时间
    time_t                  shm_dtime;  // 最后卸载时间
    time_t                  shm_ctime;  // 最后改变时间
    pid_t                    shm_cpid;    // 创建进程的PID
    pit_t                     shm_lpid;     // 最后加(卸)载进程的PID
    shmatt_t             shm_nattch; // 当前加载计数
    ...
};
int shmget(key_t key, size_t size, int shmflg);
size - 共享内存的字节数，按页向上取整。获取已有共享内存对象时可置0。
shmflg - 创建标志
0: 获取，不存在即失败。
IPC_CREATE - 创建兼获取，不存在即创建，已存在直接获取
IPC_EXCL - 不存在即创建，已存在直接报错。
在创建的时候可以加入权限，如0644|IPC_CREATE|IPC_EXCL
                                 \        /
                                  一起用可以保证创建一个新的，这就是IPC_EXCL的价值
加载共享内存到虚拟内存，家里虚拟内存和物理内存间的映射
void* shmat(int shmid, const void* shmaddr,
    int shmflags);
成功返回共享内存起始地址，失败返回void*类型的-1。
shmid - 共享内存标识
shmaddr - 共享内存起始地址，置NULL由系统内核选择
shmflags - 加载标志，可取以下值：
0: 可读可写
SHM_RDONLY: 只读
SHM_RND: 若shmaddr非空且不是页边界，则将其调整至页边界。
     |
    round圆整
卸载共享内存
int shmdt(const void* shmaddr);
成功返回0，失败返回-1。
shmat函数负责将给定共享内存映射到调用进程的虚拟内存空间，返回映射区的起始地址，同时将系统内核中共享内存对象的加载计数(shm_nattch)加1。调用进程在获得shmat函数返回的共享内存起始地址以后，就可以象访问普通内存一样访问该共享内存中的数据。shmdt函数负责从调用进程的虚拟内存中解除shmaddr所指向的映射区到共享内存的映射，同时将系统内核中共享内存对象的加载计数(shm_nattch)减1。因此加载计数为0的共享内存必定是没有任何进程使用的。shmctl(...,
IPC_RMID, ...)调用可以用于销毁共享内存，但并非真的销毁，而只是做一个销毁标记，禁止任何进程对该共享内存形成新的加载，但已有的加载依然保留。只有当其使用者们纷纷卸载，直至其加载计数降为0时，共享内存才会真的被销毁。
```

代码：wshm.c、rshm.c
通过共享内存实现进程间通信，可以直接访问由系统内核维护的公共内存区域，不需要额外构建用户缓冲区，也不需要在用户缓冲区和内核缓冲区之间来回复制数据。因此共享内存是速度最快的进程间通信机制。但是共享内存因为缺乏必要的同步机制，往往需要借助其它进程间通信策略提供某种形式的停等机制。

**注意：**共享内存需要自己销毁，就算关闭程序，共享内存还是存在。

从应用层拷贝到内核层叫写，从内核层拷贝到应用层叫读，内核所维护的就是虚拟内存所对应的地址。

```c
wshm.c

#include <stdio.h>
#include <unistd.h>
#include <sys/shm.h>
int main(void) {
    printf("创建共享内存...\n");
    key_t key = ftok(".", 100);
    if (key == -1) {
        perror("ftok");
        return -1;
    }
    int shmid = shmget(key, 4096, 0644 |
        IPC_CREAT | IPC_EXCL);
    if (shmid == -1) {
        perror("shmget");
        return -1;
    }
    printf("加载共享内存...\n");
    void* shmaddr = shmat(shmid, NULL, 0);
    if (shmaddr == (void*)-1) {
        perror("shmat");
        return -1;
    }
    printf("写入共享内存...\n");
    sprintf(shmaddr, "我是%d进程写入的数据。",
        getpid());
    printf("按<回车>卸载共享内存"
        "(0x%08x/%d)...\n", key, shmid);
    getchar();
    if (shmdt(shmaddr) == -1) {
        perror("shmdt");
        return -1;
    }
    printf("按<回车>销毁共享内存"
        "(0x%08x/%d)...\n", key, shmid);
    getchar();
    if (shmctl(shmid, IPC_RMID, NULL) == -1) {
        perror("shmctl");
        return -1;
    }
    printf("完成！\n");
    return 0;
}

rshm.c

#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <sys/shm.h>
void shmstat(int shmid) {
    struct shmid_ds shm;
    if (shmctl(shmid, IPC_STAT, &shm) == -1) {
        perror("shmctl");
        exit(-1);
    }
    printf("                  键：0x%08x\n",
        shm.shm_perm.__key);
    printf("              用户ID：%u\n",
        shm.shm_perm.uid);
    printf("                组ID：%u\n",
        shm.shm_perm.gid);
    printf("        创建者用户ID：%u\n",
        shm.shm_perm.cuid);
    printf("          创建者组ID：%u\n",
        shm.shm_perm.cgid);
    printf("                权限：%#o\n",
        shm.shm_perm.mode);
    printf("              序列号：%u\n",
        shm.shm_perm.__seq);
    printf("          大小(字节)：%u\n",
        shm.shm_segsz);
    printf("        最后加载时间：%s",
        ctime(&shm.shm_atime));
    printf("        最后卸载时间：%s",
        ctime(&shm.shm_dtime));
    printf("        最后改变时间：%s",
        ctime(&shm.shm_ctime));
    printf("         创建进程PID：%u\n",
        shm.shm_cpid);
    printf("最后加载/卸载进程PID：%u\n",
        shm.shm_lpid);
    printf("        当前加载计数：%ld\n",
        shm.shm_nattch);
}
int main(void) {
    printf("获取共享内存...\n");
    key_t key = ftok(".", 100);
    if (key == -1) {
        perror("ftok");
        return -1;
    }
    int shmid = shmget(key, 0, 0);
    if (shmid == -1) {
        perror("shmget");
        return -1;
    }
    printf("加载共享内存...\n");
    void* shmaddr = shmat(shmid, NULL, 0);
    if (shmaddr == (void*)-1) {
        perror("shmat");
        return -1;
    }
    printf("读取共享内存...\n");
    printf("%s\n", (char*)shmaddr);
    shmstat(shmid);
    printf("按<回车>卸载共享内存...\n");
    getchar();
    if (shmdt(shmaddr) == -1) {
        perror("shmdt");
        return -1;
    }
    shmstat(shmid);
    printf("完成！\n");
    return 0;
}
```

4)消息队列

```c
消息队列是由单个的类型各异的一系列消息结构组成的链表。
    消息       +->  消息
---------     |   ----------
消息类型    |    消息类型
数据长度    |    数据长度
消息数据    |    消息数据
消息指针--+   消息指针-->
   尾           首
->2|1|2|3|1|2|2|1|3|3->
  ^   ^       ^ ^
最大可发送消息字节数：8192(8K)  //每个消息包不能超过8k(两页)
最大全队列消息字节数：16384(16K)
最大全系统消息队列数：16
最大全系统消息总个数：262144
#include <sys/msg.h>
int msgget(key_t key, int msgflg);
                            |
                   0/IPC_CREAT/IPC_EXCL
发送消息
int msgsnd(
    int                msqid,   // 消息队列标识
    const void* msgp,    // ->| 消息类型(4字节) | 消息数据 |
    size_t           msgsz,   //               <--长度-->
    int                msgflg  // 0阻塞/IPC_NOWAIT非阻塞
);
成功返回0，失败返回-1。
msgsnd函数的msgp参数所指向的内存中包含4个字节大小的消息类型，其值必须大于0，但该函数的msgsz参数所表示的期望发送字节数却不包含消息类型所占的4个字节。
如果系统内核中的消息未达上限，则msgsnd函数会将欲发送消息加入消息队列并立即返回0，否则该函数会阻塞，直到系统内核允许加入新消息为止(比如有消息因被接收而离开消息队列)。若msgflg参数中包含IPC_NOWAIT位，则msgsnd函数在系统内核中的消息已达上限的情况下不会阻塞，而是返回-1，并置errno为EAGAIN。
接收消息
int msgrcv(
    int      msqid,    // 消息队列标识
    void* msgp,     // ->| 消息类型(4字节) | 消息数据缓冲区 |
    size_t msgsz,    //                   <-----长度----->
    long   msgtyp, // 消息类型
    int    msgflg   // 0阻塞/IPC_NOWAIT非阻塞/...
);
成功返回实际接收到的消息数据的长度，失败返回-1。若接收的时候，发送端的消息队列已被销毁，则返回-1，且置errno为EIDRM。
msgtyp可取以下值：
0 - 提取消息队列中的第一条消息而无论其类型。
>0 - msgflg不含MSG_EXCEPT位，提取第一条类型为msgtyp的消息，msgflg含MSG_EXCEPT位，提取第一条类型不为msgtyp的消息。
<0 - 提取队列中类型小于或等于|msgtyp|的消息，类型越小的越先被提取。
注意msgrcv函数的msgp参数所指向的内存块中包含4字节的消息类型，其值由该函数输出，但该函数的msgsz参数所表示的期望接收字节数以及该函数所返回的实际接收字节数都不包含消息类型4个字节。
若存在与msgtyp参数匹配的消息，但是数据长度大于msgsz参数，且msgflg参数包含MSG_NOERROR位，则只截取该消息数据的前msgsz字节返回，剩余部分直接丢弃；但如果msgflg参数不包含MSG_NOERROR位，则不处理该消息，直接返回-1，并置errno为E2BIG。
msgrcv函数根据msgtyp参数对消息队列中消息有选择地接收，只有满足条件的消息才会被复制到应用程序缓冲区并从内核中删除。如果满足msgtyp条件的消息不只一条，则按照先进先出的规则提取。
若消息队列中有可接收消息，则msgrcv函数会将该消息移出消息队列，并立即返回所接收到的消息数据字节数，表示接收成功，否则此函数会阻塞，直到消息队列中有可接收消息为止。若msgflg参数包含IPC_NOWAIT位，则msgrcv函数在消息队列中没有可接收消息的情况下不会阻塞，而是返回-1，并置errno为ENOMSG。
```

代码：wmsg.c、rmsg.c

```c
wmsg.c

#include <stdio.h>
#include <string.h>
#include <sys/msg.h>
int main(void) {
    printf("创建消息队列...\n");
    key_t key = ftok(".", 100);
    if (key == -1) {
        perror("ftok");
        return -1;
    }
    int msqid = msgget(key, 0644 | IPC_CREAT |
        IPC_EXCL);
    if (msqid == -1) {
        perror("msgget");
        return -1;
    }
    printf("向消息队列(0x%08x/%d)发送数据...\n",
        key, msqid);
    for (;;) {
        printf("> ");
        struct {
            long mtype;
            char mtext[1024];
        }   msgbuf = {1234, ""};
        fgets (msgbuf.mtext, sizeof(
            msgbuf.mtext) / sizeof(
            msgbuf.mtext[0]), stdin);
        if (!strcmp(msgbuf.mtext, "!\n"))
            break;
        if (msgsnd(msqid, &msgbuf, strlen(
            msgbuf.mtext) * sizeof(
            msgbuf.mtext[0]), 0) == -1) {
            perror("msgsend");
            return -1;
        }
    }
    printf("销毁消息队列(0x%08x/%d)...\n",
        key, msqid);
    if (msgctl(msqid, IPC_RMID, NULL) == -1) {
        perror("msgctl");
        return -1;
    }
    printf("完成！\n");
    return 0;
}

rmsg.c

#include <stdio.h>
#include <unistd.h>
#include <errno.h>
#include <sys/msg.h>
int main(void) {
    printf("获取消息队列...\n");
    key_t key = ftok(".", 100);
    if (key == -1) {
        perror("ftok");
        return -1;
    }
    int msqid = msgget(key, 0);
    if (msqid == -1) {
        perror("msgget");
        return -1;
    }
    printf("从消息队列(0x%08x/%d)接收消息...\n",
        key, msqid);
    for (;;) {
        struct {
            long mtype;
            char mtext[1024];
        }   msgbuf = {};
        ssize_t msgsz = msgrcv(msqid, &msgbuf,
            sizeof(msgbuf.mtext) - sizeof(
            msgbuf.mtext[0]), 1234,
            MSG_NOERROR | IPC_NOWAIT);
        if (msgsz == -1)
            if (errno == EIDRM) {
                printf("消息队列(0x%08x/%d)"
                    "已销毁！\n", key, msqid);
                break;
            }
            else
            if (errno == ENOMSG) {
                printf("现在没有消息，"
                    "干点别的...\n");
                sleep(1);
            }
            else {
                perror("msgrcv");
                return -1;
            }
        else
            printf("%04d< %s", msgsz,
                msgbuf.mtext);
    }
    printf("完成！\n");
    return 0;
}
```

5)信号量集

资源的需求者多于资源本身，如何协调有限资源在多数需求者之间的分配，以使每个资源需求者都有相对均衡的几率获得其所要求的资源。
系统内核中为每一种资源维护一个资源计数器，其值为当前空闲资源的数量，每当一个进程试图获取该种资源时，会先尝试减少其计数器的值，如果该计数器的值够减(计数器>=0)，则说明空闲资源足够，该进程即获得资源，如果该计数器的值不够减，则说明空闲资源不够，该进程即进入等待模式，等候其它拥有该种资源的进程释放资源。任何一个拥有该种资源的进程，一旦决定释放该资源，都必须将其计数器的值予以增加，以表示空闲资源量的增加，为其它等候该资源的进程提供条件。
用一个信号量表示一种类型资源，其值为该类型资源的空闲数量。用由多个信号量组成的信号量集表示多种类型的资源。
信 / 信号量0->《三国演义》=2
号 / 信号量1->《水浒传》=4
量 \ 信号量2->《红楼梦》=11
集 \ 信号量3->《四游记》=8

```c
创建或获取信号量集
#include <sys/sem.h>
int semget(key_t key, int nsems, int semflg);
成功返回信号量集标识符，失败返回-1。获取的时候不要指定信号量的个数，nsems直接给0就可以。
key - 信号量集键
nsems - 信号量个数，即资源的种类数
semflg - 创建标志，可取以下值：
0 - 获取，不存在即失败
IPC_CREAT - 创建，不存在即创建，已存在即获取
IPC_EXCL - 排斥，已存在即失败
IPC_CREAT和IPC_EXCL得配合用
操作信号量集：减操作->拥有资源...释放资源->加操作
int semop(int semid, struct sembuf* sops,
    unsigned nsops);
成功返回0，失败返回-1。
semid - 信号量集标识
sops - 操作结构数组
nsops - 操作结构数组长度
struct sembuf {
    unsigned short sem_num; // 信号量编号(集合索引)
    short                  sem_op;     // 操作数(-获取/+释放)
    short                  sem_flg;     // 操作标志
                                        // (0/IPC_NOWAIT)
};                                          |      |
                                           阻塞    非阻塞
sops指针指向一个struct sembuf类型的结构体数组，其中每个元素都是一个struct sembuf类型的结构体，该结构体包含三个字段，用于表示针对信号量集中的一个特定信号量的特定操作。
semid=0                   semid=0
---------- --semop()-> ----------
   0: 15         -1        0: 14
   1: 21                   1: 21
   2: 33         +1        2: 34
   3: 42         -1        3: 41
sops
-------------
0: {0, -1, 0}
1: {2, 1, 0}
2: {3, -1, 0}
如果sem_op字段的值为负，则从semid信号量集第sem_num个信号量的值中减去|sem_op|，以表示对资源的获取；如果不够减(信号量的值不能为负)，则此函数会阻塞，直到够减为止，以表示对资源的等待，但如果sem_flg包含IPC_NOWAIT位，则即使不够减也不会阻塞，而是返回-1，并置errno为EAGAIN。
销毁或控制信号量集
int semctl(int semid, int semnum, int cmd, ...);
成功返回0或其它与cmd有关的值，失败返回-1。
A.销毁信号量集
int semctl(int semid, 0, IPC_RMID); -> 0(成功)/-1(失败) 第二个参数没用，所以就填0就完事了
B.获取信号量集中每个信号量的值
unsigned short array[4]; // 每个信号量的值
int semctl(int semid, 0, GETALL, array); -> 0/-1
C.设置信号量集中每个信号量的值
unsigned short array[4] = {5, 5, 5, 5}; // 每个信号量的值
int semctl(int semid, 0, SETALL, array); -> 0/-1
D.获取信号量集中特定信号量的值
int semctl(int semid, int semnum, GETVAL);
    ->semid信号量集中第semnum个信号量的值/-1
E.设置信号量集中特定信号量的值
int semctl(int semid, int semnum, SETVAL, int val);
    ->0/-1
将semid信号量集中第semnum个信号量的值设置为val
```

代码：csem.c、gsem.c

```c
csem.c

#include <stdio.h>
#include <errno.h>
#include <sys/sem.h>
int pmenu(void) {
    printf("--------------------\n");
    printf("    迷你图书馆\n");
    printf("--------------------\n");
    printf("[1] 借《三国演义》\n");
    printf("[2] 还《三国演义》\n");
    printf("[3] 借《水浒传》\n");
    printf("[4] 还《水浒传》\n");
    printf("[5] 借《红楼梦》\n");
    printf("[6] 还《红楼梦》\n");
    printf("[7] 借《西游记》\n");
    printf("[8] 还《西游记》\n");
    printf("[0] 退出\n");
    printf("--------------------\n");
    printf("请选择：");
    int sel = -1;
    scanf("%d", &sel);
    return sel;
}
int pleft(int semid, unsigned short semnum) {
    int val = semctl(semid, semnum, GETVAL);
    if (val == -1) {
        perror("semctl");
        return -1;
    }
    printf("还剩%d册。\n", val);
    return 0;
}
int borrow(int semid, unsigned short semnum) {
    struct sembuf sops = {
        semnum, -1, /*0*/IPC_NOWAIT};
    if (semop(semid, &sops, 1) == -1) {
        if (errno != EAGAIN) {
            perror("semop");
            return -1;
        }
        printf("暂时无书，下回再试。\n");
        return 0;
    }
    printf("恭喜恭喜，借阅成功。\n");
    return pleft(semid, semnum);
}
int revert(int semid, unsigned short semnum) {
    struct sembuf sops = {semnum, 1, 0};
    if (semop(semid, &sops, 1) == -1) {
        perror("semop");
        return -1;
    }
    printf("好借好还，再借不难。\n");
    return pleft(semid, semnum);
}
int main(void) {
    printf("创建信号量集...\n");
    key_t key = ftok(".", 100);
    if (key == -1) {
        perror("ftok");
        return -1;
    }
    int semid = semget(key, 4, 0644 | IPC_CREAT |
        IPC_EXCL);
    if (semid == -1) {
        perror("semget");
        return -1;
    }
    printf("初始化信号量...\n");
    unsigned short semarr[] = {5, 5, 5, 5};
    if (semctl(semid, 0, SETALL, semarr) == -1) {
        perror("semctl");
        return -1;
    }
    int quit = 0;
    while (!quit) {
        int sel = pmenu();
        switch (sel) {
            case 0:
                quit = 1;
                break;
            case 1: // 借《三国演义》-> 0
            case 3: // 借《水浒传》  -> 1
            case 5: // 借《红楼梦》  -> 2
            case 7: // 借《西游记》  -> 3
                if (borrow(semid, sel / 2) == -1)
                    return -1;
                break;
            case 2: // 还《三国演义》-> 0
            case 4: // 还《水浒传》  -> 1
            case 6: // 还《红楼梦》  -> 2
            case 8: // 还《西游记》  -> 3
                if (revert(semid,
                    (sel - 1) / 2) == -1)
                    return -1;
                break;
            default:
                printf("无效选择！\n");
                scanf("%*[^\n]");
                scanf("%*c");
                break;
        }
    }
    printf("销毁信号量集...\n");
    if (semctl(semid, 0, IPC_RMID) == -1) {
        perror("semctl");
        return -1;
    }
    printf("完成！\n");
    return 0;
}

gsem.c

#include <stdio.h>
#include <errno.h>
#include <sys/sem.h>
int pmenu(void) {
    printf("--------------------\n");
    printf("    迷你图书馆\n");
    printf("--------------------\n");
    printf("[1] 借《三国演义》\n");
    printf("[2] 还《三国演义》\n");
    printf("[3] 借《水浒传》\n");
    printf("[4] 还《水浒传》\n");
    printf("[5] 借《红楼梦》\n");
    printf("[6] 还《红楼梦》\n");
    printf("[7] 借《西游记》\n");
    printf("[8] 还《西游记》\n");
    printf("[0] 退出\n");
    printf("--------------------\n");
    printf("请选择：");
    int sel = -1;
    scanf("%d", &sel);
    return sel;
}
int pleft(int semid, unsigned short semnum) {
    int val = semctl(semid, semnum, GETVAL);
    if (val == -1) {
        perror("semctl");
        return -1;
    }
    printf("还剩%d册。\n", val);
    return 0;
}
int borrow(int semid, unsigned short semnum) {
    struct sembuf sops = {
        semnum, -1, /*0*/IPC_NOWAIT};
    if (semop(semid, &sops, 1) == -1) {
        if (errno != EAGAIN) {
            perror("semop");
            return -1;
        }
        printf("暂时无书，下回再试。\n");
        return 0;
    }
    printf("恭喜恭喜，借阅成功。\n");
    return pleft(semid, semnum);
}
int revert(int semid, unsigned short semnum) {
    struct sembuf sops = {semnum, 1, 0};
    if (semop(semid, &sops, 1) == -1) {
        perror("semop");
        return -1;
    }
    printf("好借好还，再借不难。\n");
    return pleft(semid, semnum);
}
int main(void) {
    printf("获取信号量集...\n");
    key_t key = ftok(".", 100);
    if (key == -1) {
        perror("ftok");
        return -1;
    }
    int semid = semget(key, 0, 0);
    if (semid == -1) {
        perror("semget");
        return -1;
    }
    int quit = 0;
    while (!quit) {
        int sel = pmenu();
        switch (sel) {
            case 0:
                quit = 1;
                break;
            case 1: // 借《三国演义》-> 0
            case 3: // 借《水浒传》  -> 1
            case 5: // 借《红楼梦》  -> 2
            case 7: // 借《西游记》  -> 3
                if (borrow(semid, sel / 2) == -1)
                    return -1;
                break;
            case 2: // 还《三国演义》-> 0
            case 4: // 还《水浒传》  -> 1
            case 6: // 还《红楼梦》  -> 2
            case 8: // 还《西游记》  -> 3
                if (revert(semid,
                    (sel - 1) / 2) == -1)
                    return -1;
                break;
            default:
                printf("无效选择！\n");
                scanf("%*[^\n]");
                scanf("%*c");
                break;
        }
    }
    printf("完成！\n");
    return 0;
}
```

**各种进程间通信总结**

命令行参数和环境变量(给main函数传参)
    初始化设置
回收进程退出码(接收main函数的返回值或者exit函数的参数)
    获得终止信息
内存映射文件
    通过内存的方式操作共享文件，读写磁盘数据
    速度慢但持久性好
信号
    简单，异步，信息量有限，效率不高，可靠性不佳
有名管道或本地套接字
    非近亲进程之间的中等规模数据通信
无名管道
    近亲进程之间的中等规模数据通信
共享内存
    大数据量的快速数据通信
    缺乏同步机制，需要依赖其它IPC机制实现同步
消息队列
    天然的同步性，根据类型做细分
    适用于中等规模数据通信
信号量集
    多数进程竞争少数资源

6)IPC命令
查看IPC对象
ipcs -m, m=memory, 共享内存对象
ipcs -q, q=queue, 消息队列对象
ipcs -s, s=semphore, 信号量集对象
ipcs -a, a=all, 全部IPC对象
删除IPC对象
ipcrm -m <共享内存对象标识>
ipcrm -q <消息队列对象标识>
ipcrm -s <信号量集对象标识>

## 十三、网络通信

### 13.1 网络和网络协议

1)什么是计算机网络？
计算机网络是指将地理位置不同的具有独立功能的多台计算机及其外部设备，通过有形或无形的通信线路连接起来，在网络操作系统、网络管理软件及网络通信协议的管理和协调下，实现资源共享和信息传递的计算机系统。
2)什么是网络协议？
网络协议是一种特殊的软件，是计算机网络实现其功能的最基本的机制。网路协议的本质就是规则，即各种硬件和软件必须遵循的共同守则。网络协议并不是一套单独的软件，它融合于所有涉及网络通信的软件甚至硬件之中，因此可以说协议于网络中无处不在。
3)什么是协议栈？
为了减少网络设计的复杂性，绝大多数网络采用分层设计的方法。所谓分层设计，就是按照信息流动的过程将网络的整体功能分解为一个个的功能层，不同机器上的同等功能层之间采用相同的协议，同一机器上相邻功能层之间通过接口进行信息传递。各层的协议和接口统称为协议栈。
ISO(国际标准化组织)/OSI(Open System Interconnection, 开放系统互联)网络协议模型：
应用层：业务逻辑                             \
表示层：数据的表现形式                   | -> 应用层
会话层：建立、管理和终止通信过程 /

传输层：源到目的地的点对点传输
网络层：路径选择、路由、寻址等网络结构拓扑
数据链路层：物理寻址、数据通道、错误检测等通信路径
物理层：在数据和电平信号之间进行转换
比喻：
买点心（应用层：业务需求）
选择包装（表示层：数据形式）
选择快递公司（会话层：通信过程）
填写收寄单（传输层：点对点）
选择路径（网络层：通路）
中途周转（数据链路层：多点连线）
送货过程（物理层：实际通信）
4)TCP/IP协议栈
传输层：TCP、UDP
网络层：IP、ICMP、IGMP
链路层：ARP、RARP (把IP地址与mac地址映射)
5)消息包和数据流
应用层：HTTP请求=用户数据包
传输层：TCP头+用户数据包=TCP包
网络层：IP头+TCP包=IP包
链路层：以太网头+IP包+以太网尾=以太网帧
物理层：以太网帧->电平信号
                                    |   ^
                                   v    |
                                传输线路
发送数据流：消息自协议栈顶层向底层流动，逐层打包。
接收数据流：消息自协议栈底层向顶层流程，逐层解析。
6)IP地址
IP地址(Internet Protocol Address，互联网协议地址)是一种地址格式，为互联网上的每个网络和主机分配一个逻辑地址，其目的是消除物理地址的差异性。
IP地址的计算机内部用一个网络字节序的32位(4个字节)无符号整数表示。通常习惯将其表示为点分十进制整数字符串的形式。例如：
点分十进制整数字符串：1.2.3.4
32位(4个字节)无符号整数：0x01020304
内存布局：| 0x01 | 0x02 | 0x03 | 0x04 |
                      低地址--------------->高地址
网络字节序就是大端字节序，高位在低地址，低位在高地址。
中国北京市东城区珠市口大街珍贝大厦三层第一教室
一台计算机的IP地址=网络地址+主机地址
A级地址：以0为首8位网络地址+24位主机地址
B级地址：以10为首16位网络地址+16位主机地址
C级地址：以110为首24位网络地址+8位主机地址
D级地址：以1110为首的32为多(组)播地址
例如：某台计算机的IP地址为192.168.182.48，其网络地址和主机地址分别为何？
           192            168            182              48
11000000 10101000 10110110 00110000
以110为首，C级地址，网络地址是192.168.182.0，主机地址是48。
主机IP地址 & 子网掩码 = 网络地址
主机IP地址 & ~子网掩码 = 主机地址
例如：主机IP地址192.168.182.48，子网掩码255.255.255.0，其网络地址和主机地址分别为何？
192.168.182.48 & 255.255.255.0 = 192.168.182.0
192.168.182.48 & 0.0.0.255 = 0.0.0.48

### 13.2 套接字

1)什么是套接字？
Socket，电源插座->套接字
一个由系统内核负责维护，通过文件描述符访问的对象， 可用于在同一台机器或不同机器中的进程之间实现通信。
进程表项
    文件描述符表
    0: 文件描述符标志 | * -> 标准输入文件表项 -> 键盘
    1: 文件描述符标志 | * -> 标准输出文件表项 -> 显示器
    2: 文件描述符标志 | * -> 标准错误文件表项 -> 显示器
    3: 文件描述符标志 | * -> 套接字对象 -> 网卡
           应用程序                       应用程序
                 v                                   v
磁盘文件的文件描述符    表示网络的文件描述符
                 v                                   v
           文件对象                      套接字对象
                 v                                   v
           文件系统                      网络协议栈
                 v                                   v
           磁盘设备                       网络设备
套接字也可以被视为是围绕表示网络的文件描述符的一套函数库。调用其中的函数就可以访问网络上的数据，实现不同主机间的通信功能。
2)绑定和连接
   套接字就是系统内核内存中的一块数据——逻辑对象
                                                                            | 绑定(bind)
包含了IP地址和端口号等参数的网络设备——物理对象
互联网
     | <-网络地址：192.168.182.0
子网络
     | <-主机地址：0.0.0.48
计算机
     | <-端口号：80
  应用
通过IP地址(网络地址+主机地址)端口号就可以唯一定位互联网上的一个通信引用。
主机A
应用程序
      |
逻辑对象(套接字)
      | 绑定(bind)                  连接(connection)
物理对象(IP地址和端口号)---------物理对象(IP地址和端口号)
                                                             | 绑定(bind)
                                                       逻辑对象(套接字)
                                                             |
                                                       应用程序
                                                       主机B

3)常用函数

```c
创建套接字
#include <sys/socket.h>
int socket(int domain, int type, int protocol);
成功返回套接字描述符，失败返回-1。
domain - 通信域，即协议族，可取以下值：
PF_LOCAL/PF_UNIX: 本地通信，进程间通信
PF_INET: 互联网通信
PF_PACKET: 底层包通信（嗅探器、端口扫描）//嵌入式用不到
type - 套接字类型，可取以下值：
SOCK_STREAM: 流式套接字，使用TCP协议
SOCK_DGRAM: 数据报式套接字，使用UDP协议   //本地套接字只能用UDP
SOCK_RAW: 原始套接字，使用自定义协议
protocol - 特殊协议
                  对于流式套接字和数据报式套接字，取0
套接字描述符与文件描述符在逻辑层面是一致的，所有关于文件描述符的规则对于套接字描述符也同样成立。同样也通过close函数关闭套接字，即释放内核中的有关资源。
基本地址结构：
struct sockaddr {
    sa_family_t sa_family; // 地址族
    char sa_data[14]; // 地址值
};
基本地址结构仅用于给函数传参时做强制类型转换。
本地地址结构：
#include <sys/un.h>
struct sockaddr_un {
    sa_family_t sun_family; // 地址族
            // (AF_LOCAL/AF_UNIX) 实际上这两个的值和PF_LOCAL/PF_UNIX是一样的
    char sun_path[]; // 套接字文件路径
};          //本地套接字文件在绑定的时候会自动建立
网络地址结构：
#include <sys/in.h>
struct sockaddr_in {
    sa_family_t sin_family; // 地址族(AF_INET)
    in_port_t sin_port; // 端口号(网络字节序)
    struct in_addr sin_addr; // IP地址
};
struct in_addr {
    in_addr_t s_addr; // 网络字节序32位无符号整数形式的
                                   // IP地址
};
typedef uint32_t in_addr_t;
typedef uint16_t in_port_t;
小端字节序的主机A              大端字节序的主机B
short a = 0x1234;
| 0x34 | 0x12 |                    | 0x34 | 0x12 |
    低         高                             低         高
                                             a: 0x3412
发送：主机字节序->网络(大端)字节序
接收：网络(大端)字节序->主机字节序
小端字节序的主机A             大端字节序的主机B
0x1234
主：0x34 | 0x12
   L        H
网：0x12 | 0x34      ->      网：0x12 | 0x34
                                 L      H
                            主：0x12 | 0x34
                                 0x1234
                           -------------------
                             小端字节序的主机C
                     ->      网：0x12 | 0x34
                                 L       H
                             主：0x34 | 0x12
                                  0x1234
将套接字对象和自己的地址结构绑定在一起
int bind(int sockfd, const struct sockaddr* addr,
    socklen_t addrlen);
成功返回0，失败返回-1。
sockfd - 套接字描述符
addr - 自己的地址结构
addrlen - 地址结构字节数
addr->sa_family: AF_LOCAL/AF_UNIX
     ((struct sockaddr_un*)addr)->sun_path: 套接字文件
addr->sa_family: AF_INET
     ((struct sockaddr_in*)addr)->sin_port/sin_addr:
                                          IP地址和端口号
将套接字对象所代表的物理对象和对方的地址结构连接在一起
int connect(int sockfd, const struct sockaddr* addr,
    socklen_t addrlen);
成功返回0，失败返回-1。
sockfd - 套接字描述符
addr - 对方的地址结构
addrlen - 地址结构字节数
通过套接字描述符接收和发送数据的过程完全与通过文件描述符读取和写入数据的过程完全一样。
ssize_t read(int sockfd, void* buf, size_t count);
ssize_t write(int sockfd, const void* buf, size_t count);
字节序转换
通过网络传输多字节整数，需要在发送前转换为网络字节序，在接收后转换为主机字节序。(单字节不用考虑，浮点数不用考虑，字符串也不用考虑，因为编码规则已经保证了字节序)
#include <arpa/inet.h>
uint32_t htonl(uint32_t hostlong);
uint16_t htons(uint16_t hostshort);
uint32_t ntohl(uint32_t netlong);
uint16_t ntohs(uint16_t netshort);
h - host，主机(字节序)
to - 到，把...转换到...
n - network，网络(字节序)
l - long版本，32位无符号整数
s - short版本，16位无符号整数
IP地址转换
(网络字节序32位无符号)整数<=>(点分十进制)字符串
#include <arpa/inet.h>
in_addr_t inet_addr(const char* cp); // 串->数
int inet_aton(const char* cp, struct in_addr* inp);
// 串->数
转换成功返回0，失败返回-1。
思考：inet_addr和inet_aton的功能一样，那为什么还要多设一个呢?因为inet_addr可以通过返回值来判断转换是否成功。
char* inet_ntoa(struct in_addr in); // 数->串
转换成功返回字符串指针，失败返回NULL。

```

基于本地套接字的进程间通信：
服务器：提供业务服务的计算机程序。
客户机：请求业务服务的计算机程序。
服务器                                                       客户机
创建套接字(socket)                           创建套接字(socket)
准备地址结构(sockaddr_un)            准备地址结构(sockaddr_un)
绑定地址(bind)                                    建立连接(connect)
接收请求(read)                                    发送请求(write)
业务处理(...)                                         等待处理(...)
发送响应(write)                                   接收响应(read)
关闭套接字(close)                               关闭套接字(close)

代码：locsvr.c、loccli.c

**小知识：**atoi把字符串变成相对应的数，这个数是主机字节序。INADDR_ANY这个宏其实是个0，就是任意IP地址，我不指定。正因为是0，所以可以省略主机字节序转网络字节序的步骤。

```c
locsvr.c

#include <stdio.h>
#include <unistd.h>
#include <sys/socket.h>
#include <sys/un.h>
#define SOCK_FILE "mysock"
int main(void) {
    printf("服务器：创建本地套接字\n");
    int sockfd = socket(PF_LOCAL, SOCK_DGRAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return -1;
    }
    printf("服务器：准备地址并绑定\n");
    struct sockaddr_un addr;
    addr.sun_family = AF_LOCAL;
    strcpy(addr.sun_path, SOCK_FILE);
    if (bind(sockfd, (struct sockaddr*)&addr,
        sizeof(addr)) == -1) {
        perror("bind");
        return -1;
    }
    printf("服务器：接收数据\n");
    for (;;) {
        char buf[1024] = {};
        ssize_t rb = read(sockfd, buf, sizeof(
            buf) - sizeof(buf[0]));
        if (rb == -1) {
            perror("read");
            return -1;
        }
        if (!strcmp(buf, "!\n"))
            break;
        printf("< %s", buf);
    }
    printf("服务器：关闭套接字\n");
    if (close(sockfd) == -1) {
        perror("close");
        return -1;
    }
    printf("服务器：删除套接字文件\n");
    if (unlink(SOCK_FILE) == -1) {
        perror("unlink");
        return -1;
    }
    printf("服务器：完成\n");
    return 0;
}

loccli.c

#include <stdio.h>
#include <unistd.h>
#include <sys/socket.h>
#include <sys/un.h>
#define SOCK_FILE "mysock"
int main(void) {
    printf("客户机：创建本地套接字\n");
    int sockfd = socket(PF_LOCAL, SOCK_DGRAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return -1;
    }
    printf("客户机：准备地址并连接\n");
    struct sockaddr_un addr;
    addr.sun_family = AF_LOCAL;
    strcpy(addr.sun_path, SOCK_FILE);
    if (connect(sockfd, (struct sockaddr*)&addr,
        sizeof(addr)) == -1) {
        perror("connect");
        return -1;
    }
    printf("客户机：发送数据\n");
    for (;;) {
        printf("> ");
        char buf[1024];
        fgets(buf, sizeof(buf) / sizeof(buf[0]),
            stdin);
        if (write(sockfd, buf, strlen(buf) *
            sizeof(buf[0])) == -1) {
            perror("write");
            return -1;
        }
        if (!strcmp(buf, "!\n"))
            break;
    }
    printf("客户机：关闭套接字\n");
    if (close(sockfd) == -1) {
        perror("close");
        return -1;
    }
    printf("客户机：完成\n");
    return 0;
}
```

基于网络套接字的进程间通信：
服务器                                     客户机
创建套接字(socket)                创建套接字(socket)
准备地址结构(sockaddr_in)   准备地址结构(sockaddr_in)
绑定地址(bind)                       建立连接(connect)
接收请求(read)                       发送请求(write)
业务处理(...)                            等待处理(...)
发送响应(write)                      接收响应(read)
关闭套接字(close)                   关闭套接字(close)
代码：netsvr.c、netcli.c

```c
netsvr.c

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
int main(int argc, char* argv[]) {
    if (argc < 2) {
        fprintf(stderr, "用法：%s <端口号>\n",
            argv[0]);
        return -1;
    }
    printf("服务器：创建网络套接字\n");
    int sockfd = socket(PF_INET, SOCK_DGRAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return -1;
    }
    printf("服务器：准备地址并绑定\n");
    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(atoi(argv[1]));
    addr.sin_addr.s_addr = INADDR_ANY;
    if (bind(sockfd, (struct sockaddr*)&addr,
        sizeof(addr)) == -1) {
        perror("bind");
        return -1;
    }
    printf("服务器：接收数据\n");
    for (;;) {
        char buf[1024] = {};
        ssize_t rb = read(sockfd, buf, sizeof(
            buf) - sizeof(buf[0]));
        if (rb == -1) {
            perror("read");
            return -1;
        }
        if (!strcmp(buf, "!\n"))
            break;
        printf("< %s", buf);
    }
    printf("服务器：关闭套接字\n");
    if (close(sockfd) == -1) {
        perror("close");
        return -1;
    }
    printf("服务器：完成\n");
    return 0;
}

netcli.c

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
int main(int argc, char* argv[]) {
    if (argc < 3) {
        fprintf(stderr, "用法：%s <IP地址> "
            "<端口号>\n", argv[0]);
        return -1;
    }
    printf("客户机：创建网络套接字\n");
    int sockfd = socket(PF_INET, SOCK_DGRAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return -1;
    }
    printf("客户机：准备地址并连接\n");
    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(atoi(argv[2]));
    addr.sin_addr.s_addr = inet_addr(argv[1]);
    if (connect(sockfd, (struct sockaddr*)&addr,
        sizeof(addr)) == -1) {
        perror("connect");
        return -1;
    }
    printf("客户机：发送数据\n");
    for (;;) {
        printf("> ");
        char buf[1024];
        fgets(buf, sizeof(buf) / sizeof(buf[0]),
            stdin);
        if (write(sockfd, buf, strlen(buf) *
            sizeof(buf[0])) == -1) {
            perror("write");
            return -1;
        }
        if (!strcmp(buf, "!\n"))
            break;
    }
    printf("客户机：关闭套接字\n");
    if (close(sockfd) == -1) {
        perror("close");
        return -1;
    }
    printf("客户机：完成\n");
    return 0;
}
```

### 13.3 基于并发的TCP客户机/服务器模型

1)TCP协议的基本特征

```c
A.面向连接：参与通信的双发在正式通信之前需要先建立连接，已形成一条虚拟电路，所有的后续通信都在这条虚电路上完成。类似于电话通信业务。正式通话之前要先拨号，拨通了才能讲话。拨号的过程就是一个建立连接的过程。
三路握手
    客户机                           服务器
发起连接请求 --------SYN(n)-------->  被动侦听
   等待应答     <-ACK(n+1)+SYN(m)-   可以接受
   反向确认     ------ACK(m+1)------> 连接成功
一旦三路握手完成，客户机和服务器的网络协议栈中就会保存有关连接的信息，此后的通信内容全部基于此连接实现数据传输。通信过程中任何原因导致的连接中断，都无法再继续通信，除非重新建立连接。
B.可靠传输：超时重传。每次发送一个数据包，对方都需要在一个给定的时间窗口内予以应答，如果超过时间没有收到对方的应答，发送方就会重发该数据包，只有重试过足够多的次数依然失败才会最终放弃。
C.保证顺序：发送端为每一个数据包编制序列号，接收端会根据序列号对所接收到的数据包进行重排，避免重复和乱序。
D.流量控制：协议栈底层在从另一端接收数据时，会不断告知对方它能够接收多少字节的数据，即所谓通告窗口。任何时候，这个窗口都反映了接收缓冲区可用空间的大小，从而确保不会因为发送方发送数据过快或过慢导致接收缓冲区出现上溢出或下溢出。
E.流式传输：以字节流形式传输数据，数据包在传输过程中没有记录边界。应用程序需要根据自己的规则来划分出数据包的记录边界。
a)定长记录
b)不定长记录加分隔符
c)定长长度加不定长记录
F.全双工：在给定的连接上，应用程序在任何时候都既可以发送数据也可以接收数据。因此TCP必须跟踪每个方向上数据流的状态信息，如序列号和通告窗口大小等。
```

2)TCP连接的生命周期

```c
被动打开：通过侦听套接字感知其它主机发起的连接请求。
三路握手：TCP连接的建立过程。
| TCP包头 | TCP包体 |
<20字节>
含有6个标志位：SYN/ACK/FIN/RST/...
发送序列号和应答序列号
...
数据传输：超时重传、流量控制、面向字节流、全双工
终止连接：
 客户机                         服务器
主动关闭 ---------FIN(n)-------> 被动关闭
等待应答 <-----ACK(n+1)------   关闭应答
确定关闭 <--------FIN(m)-------  已经关闭
关闭应答 ------ACK(m+1)----->  等待应答
```

3)常用函数

```c
在指定套接字上启动对连接请求的侦听，即将该套接字置为被动模式，因为套接字都缺省为主动模式。
int listen(int sockfd, int backlog);
成功返回0，失败返回-1。
sockfd - 套接字描述符
backlog - 未决连接请求队列的最大长度(尽量设置1024以上)
在指定的侦听套接字上等待并接受连接请求
int accept(int sockfd, struct sockaddr* addr,
    size_t* addrlen);
成功返回连接套接字描述符用于后续通信，失败返回-1。
sockfd - 侦听套接字描述符
addr - 输出连接请求发起者的地址信息
addrlen - 输入输出连接请求发起者地址信息的字节数(即是输入又是输出，输入的时候按照实际结构体的大小来传，输出的时候是它实际往里填的字节数)
该函数由TCP服务器调用，返回排在已决连接队列首部的连接套接字对象的描述符，若已决连接队列为空，该函数会阻塞。
^ ^ ^
非并发的TCP服务器
创建套接字(socket)
绑定地址(bind)
启动侦听(listen)
等待连接(accept)<---+
接收请求(read)<-+   |
业务处理(...)   |   |
发送响应(write)--+--+

并发的TCP服务器
创建套接字(socket)
绑定地址(bind)
启动侦听(listen)
等待连接(accept)<---+
                   |
产生客户子进程(fork)-+
    接收请求(read)<-+
    业务处理(...)    |
    发送响应(write)--+
接收数据
ssize_t recv(int sockfd, void* buf, size_t len, int flags);
flags - 接收标志，取0等价于read
    MSG_DONTWAIT: 非阻塞接收
    对于阻塞模式，当接收缓冲区为空时，该函数会阻塞，直到
    接收缓冲区不空为止。如果使用了此标志位，当接收缓冲区
    为空时，该函数会返回-1，并置errno为EAGAIN或
    EWOULDBLOCK(windows上的)。
    MSG_OOB: 接收带外数据。
    MSG_PEEK: 瞄一眼数据，只讲接收缓冲区中的数据复制到
    buf缓冲区中，但并不将其从接收缓冲区中删除。
    MSG_WAITALL: 接收到所有期望接收的数据才返回，如果
    接收缓冲区中的数据不到len个字节，该函数会阻塞，直到可
    接收到len个字节为止。
发送数据
ssize_t send(int sockfd, const void* buf, size_t len,
    int flags);
flags - 接收标志，取0等价于write
     MSG_DONTWAIT: 非阻塞发送
     对于阻塞模式，当发送缓冲区的空余空间不足以容纳期望发送的字节数时，该函数会阻塞，直到发送缓冲区的空余空间
     足以容纳期望发送的字节数为止。如果使用了此标志位，能
     发送多少字节就发送多少字节，不会阻塞，甚至可能返回0
     表示发送缓冲区满，无法发送数据。
     MSG_OOB: 发送带外数据。
     MSG_DONTROUT: 不查路由表，直接在本地网中寻找目
     的主机。
```

代码：tcpsvr.c、tcpcli.c

```c
tcpsvr.c

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <signal.h>
#include <wait.h>
#include <errno.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
void sigchld(int signum) {
    for (;;) {
        pid_t pid = waitpid(-1, NULL, WNOHANG);
        if (pid == -1) {
            if (errno != ECHILD) {
                perror("waitpid");
                exit(-1);
            }
            printf("服务器："
                "全部子进程都已退出\n");
            break;
        }
        if (pid)
            printf("服务器："
                "发现%d子进程退出\n", pid);
        else {
            printf("服务器："
                "暂时没有子进程退出\n");
            break;
        }
    }
}
int client(int connfd) {
    for (;;) {
        printf("%d：接收请求\n", getpid());
        char buf[1024];
        ssize_t rb = recv(connfd, buf,
            sizeof(buf), 0);
        if (rb == -1) {
            perror("recv");
            return -1;
        }
        if (rb == 0) {
            printf("%d：客户机已关闭\n",
                getpid());
            break;
        }
        printf("%d：发送响应\n", getpid());
        if (send(connfd, buf, rb, 0) == -1) {
            perror("send");
            return -1;
        }
    }
    return 0;
}
int main(int argc, char* argv[]) {
    if (argc < 2) {
        fprintf(stderr, "用法：%s <端口号>\n",
            argv[0]);
        return -1;
    }
    if (signal(SIGCHLD, sigchld) == SIG_ERR) {
        perror("signal");
        return -1;
    }
    printf("服务器：创建网络套接字\n");
    int sockfd = socket(PF_INET, SOCK_STREAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return -1;
    }
    printf("服务器：准备地址并绑定\n");
    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(atoi(argv[1]));
    addr.sin_addr.s_addr = INADDR_ANY;
    if (bind(sockfd, (struct sockaddr*)&addr,
        sizeof(addr)) == -1) {
        perror("bind");
        return -1;
    }
    printf("服务器：侦听套接字\n");
    if (listen(sockfd, 1024) == -1) {
        perror("listen");
        return -1;
    }
    for (;;) {
        printf("服务器：等待连接\n");
        struct sockaddr_in addrcli = {};
        socklen_t addrlen = sizeof(addrcli);
        int connfd = accept(sockfd,
            (struct sockaddr*)&addrcli,
            &addrlen);
        if (connfd == -1) {
            perror("accept");
            return -1;
        }
        printf("服务器：客户机%s:%u\n",
            inet_ntoa(addrcli.sin_addr),
            ntohs(addrcli.sin_port));
        pid_t pid = fork();
        if (pid == -1) {
            perror("fork");
            return -1;
        }
        if (pid == 0) {
            if (close(sockfd) == -1) {
                perror("close");
                return -1;
            }
            if (client(connfd) == -1)
                return -1;
            if (close(connfd) == -1) {
                perror("close");
                return -1;
            }
            printf("%d：完成\n", getpid());
            return 0;
        }
        if (close(connfd) == -1) {
            perror("close");
            return -1;
        }
    }
    return 0;
}

tcpcli.c

#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <errno.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
int main(int argc, char* argv[]) {
    if (argc < 3) {
        fprintf(stderr, "用法：%s <IP地址> "
            "<端口号>\n", argv[0]);
        return -1;
    }
    printf("客户机：创建网络套接字\n");
    int sockfd = socket(PF_INET, SOCK_STREAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return -1;
    }
    printf("客户机：准备地址并连接\n");
    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(atoi(argv[2]));
    addr.sin_addr.s_addr = inet_addr(argv[1]);
    if (connect(sockfd, (struct sockaddr*)&addr,
        sizeof(addr)) == -1) {
        perror("connect");
        return -1;
    }
    for (;;) {
        printf("> ");
        char buf[1024];
        fgets(buf, sizeof(buf) / sizeof(buf[0]),
            stdin);
        if (!strcmp(buf, "!\n"))
            break;
        printf("客户机：发送请求\n");
        if (send(sockfd, buf, strlen(buf) *
            sizeof(buf[0]), 0) == -1) {
            perror("send");
            return -1;
        }
        printf("客户机：接收响应\n");
        ssize_t rb = recv(sockfd, buf,
            sizeof(buf) - sizeof(buf[0]), 0);
        if (rb == 0) {
            printf("客户机：服务器已关闭\n");
            break;
        }
        buf[rb / sizeof(buf[0])] = '\0';
        printf("< %s", buf);
    }
    printf("客户机：关闭套接字\n");
    if (close(sockfd) == -1) {
        perror("close");
        return -1;
    }
    printf("完成\n");
    return 0;
}
```

### 13.4 基于迭代的UDP客户机/服务器模型

1)UDP协议的基本特征
A.面向无连接：参与通信的主机之间不需要专门建立和维护逻辑的连接通道。一个UDP套接字可以和任意其它UDP套接字通信，而不必受连接的限制。
B.不可靠传输：没有超时重传机制。可能导致数据丢失。
C.不保证顺序：没有序列号，也不进行序列重组。可能产生数据包重复和乱序。
D.无流量控制：没有通告窗口，通信参与者完全不知道对方的接受能力。可能造成数据溢出。
E.记录式传输：以消息报文的形式传输数据，数据包在传输过程中有记录边界。应用程序无需划分数据包边界。
F.全双工
通过在可靠性方面的部分牺牲换取高速度。

2)常用函数

```c
ssize_t sendto(int sockfd, const void* buf, size_t len,
    int flags, const struct sockaddr* dest_addr,
    socklen_t addrlen);
dest_addr - 数据包接收者地址结构
addrlen - 数据包接收者地址结构字节数
(如果这个函数用于TCP，这个函数的后两个参数就会被忽略)
ssize_t recvfrom(int sockfd, void* buf, size_t len,
    int flags, struct sockaddr* src_addr,
    socklen_t* addrlen);
src_addr - 输出数据包发送者地址结构
addrlen - 输出数据包发送者地址结构字节数
```

代码：udpsvr.c、udpcli.c

```c
udpsvr.c

#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
int main(int argc, char* argv[]) {
    if (argc < 2) {
        fprintf(stderr, "用法：%s <端口号>\n",
            argv[0]);
        return -1;
    }
    printf("服务器：创建网络套接字\n");
    int sockfd = socket(PF_INET, SOCK_DGRAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return -1;
    }
    printf("服务器：准备地址并绑定\n");
    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(atoi(argv[1]));
    addr.sin_addr.s_addr = INADDR_ANY;
    if (bind(sockfd, (struct sockaddr*)&addr,
        sizeof(addr)) == -1) {
        perror("bind");
        return -1;
    }
    for (;;) {
        printf("服务器：接收请求\n");
        char buf[1024];
        struct sockaddr_in addrcli = {};
        socklen_t addrlen = sizeof(addrcli);
        ssize_t rb = recvfrom(sockfd, buf,
            sizeof(buf), 0,
            (struct sockaddr*)&addrcli,
            &addrlen);
        if (rb == -1) {
            perror("recvfrom");
            return -1;
        }
        printf("服务器：向%s:%u发送响应\n",
            inet_ntoa(addrcli.sin_addr),
            ntohs(addrcli.sin_port));
        if (sendto(sockfd, buf, rb, 0,
            (struct sockaddr*)&addrcli,
            addrlen) == -1) {
            perror("sendto");
            return -1;
        }
    }
    return 0;
}

udpcli.c
    
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
int main(int argc, char* argv[]) {
    if (argc < 3) {
        fprintf(stderr, "用法：%s <IP地址> "
            "<端口号>\n", argv[0]);
        return -1;
    }
    printf("客户机：创建网络套接字\n");
    int sockfd = socket(PF_INET, SOCK_DGRAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return -1;
    }
    printf("客户机：准备地址\n");
    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(atoi(argv[2]));
    addr.sin_addr.s_addr = inet_addr(argv[1]);
    for (;;) {
        printf("> ");
        char buf[1024];
        fgets(buf, sizeof(buf) / sizeof(buf[0]),
            stdin);
        if (!strcmp(buf, "!\n"))
            break;
        printf("客户机：向%s:%u发送请求\n",
            inet_ntoa(addr.sin_addr),
            ntohs(addr.sin_port));
        if (sendto(sockfd, buf, strlen(buf) *
            sizeof(buf[0]), 0,
            (struct sockaddr*)&addr,
            sizeof(addr)) == -1) {
            perror("sendto");
            return -1;
        }
        printf("客户机：接收响应\n");
        ssize_t rb = recv(sockfd, buf,
            sizeof(buf) - sizeof(buf[0]), 0);
        if (rb == -1) {
            perror("recv");
            return -1;
        }
        buf[rb / sizeof(buf[0])] = '\0';
        printf("< %s", buf);
    }
    printf("客户机：关闭套接字\n");
    if (close(sockfd) == -1) {
        perror("close");
        return -1;
    }
    printf("客户机：完成\n");
    return 0;
}
```

针对UDP套接字的connect函数并不象TCP套接字的connect函数一样通过三路握手过程建立所谓的虚电路连接，而仅仅是将传递给该函数的对方地址结构缓存在套接字对象中。此后通过该套接字发送数据时，可以不适用sendto函数，而直接调用send函数，有关接收方的地址信息从套接字对象的地址缓存中提取即可。
代码：concli.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
int main(int argc, char* argv[]) {
    if (argc < 3) {
        fprintf(stderr, "用法：%s <IP地址> "
            "<端口号>\n", argv[0]);
        return -1;
    }
    printf("客户机：创建网络套接字\n");
    int sockfd = socket(PF_INET, SOCK_DGRAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return -1;
    }
    printf("客户机：准备地址并连接\n");
    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(atoi(argv[2]));
    addr.sin_addr.s_addr = inet_addr(argv[1]);
    if (connect(sockfd, (struct sockaddr*)&addr,
        sizeof(addr)) == -1) {
        perror("connect");
        return -1;
    }
    for (;;) {
        printf("> ");
        char buf[1024];
        fgets(buf, sizeof(buf) / sizeof(buf[0]),
            stdin);
        if (!strcmp(buf, "!\n"))
            break;
        printf("客户机：向%s:%u发送请求\n",
            inet_ntoa(addr.sin_addr),
            ntohs(addr.sin_port));
        if (send(sockfd, buf, strlen(buf) *
            sizeof(buf[0]), 0) == -1) {
            perror("send");
            return -1;
        }
        printf("客户机：接收响应\n");
        ssize_t rb = recv(sockfd, buf,
            sizeof(buf) - sizeof(buf[0]), 0);
        if (rb == -1) {
            perror("recv");
            return -1;
        }
        buf[rb / sizeof(buf[0])] = '\0';
        printf("< %s", buf);
    }
    printf("客户机：关闭套接字\n");
    if (close(sockfd) == -1) {
        perror("close");
        return -1;
    }
    printf("客户机：完成\n");
    return 0;
}
```

### 13.5 域名解析（Domain Name Service, DNS）

字符串形式的域名
(www.tmooc.cn)
              |
              v
      DNS服务器 
              |
              v
整数形式的IP地址
(202.141.55.23)-->套接字编程

根据主机域名获取信息

```c
#include <netdb.h>
struct hostent* gethostbyname(const char* name);
成功返回主机信息条目指针，失败返回NULL。
name - 主机域名(字符串)
hostent
    h_name -> xxx\0 - 主机官方名
    h_aliases -> * * * ... NULL - 别名表
    h_addrtype -> AF_INET - 地址类型
    h_length -> 4 - 地址字节数
    h_addr_list -> * * * ... NULL - 地址表
                    |
                    v
              struct in_addr
                  s_addr - 网络字节序32位无符号整数
```

代码：dns.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <arpa/inet.h>
#include <netdb.h>
int main(int argc, char* argv[]) {
    if (argc < 2) {
        fprintf(stderr, "用法：%s <主机域名>\n",
            argv[0]);
        return -1;
    }
    struct hostent* host = gethostbyname(
        argv[1]);
    if (!host) {
        perror("gethostbyname");
        return -1;
    }
    if (host->h_addrtype != AF_INET) {
        fprintf(stderr, "非IPv4地址！\n");
        return -1;
    }
    printf("主机官方名：\n");
    printf("\t%s\n", host->h_name);
    printf("主机别名表：\n");
    for (char** pp = host->h_aliases; *pp; ++pp)
        printf("\t%s\n", *pp);
    printf("主机地址表：\n");
    for (struct in_addr** pp =
        (struct in_addr**)host->h_addr_list;
        *pp; ++pp)
        printf("\t%s\n", inet_ntoa(**pp));
    return 0;
}
```

### 13.6 获取HTTP服务器上的页面内容

```c
HTTP, Hyper Text Transform Protocol，超文本传输协议
www服务器通过http协议提供页面服务。
HTTP - 应用层协议：| HTTP包头 | HTTP包体 |
  |
 TCP - 传输层协议：| TCP包头  | HTTP包头 | HTTP包体 |
  |
  IP - 网络层协议：...
  |
  ...
HTTP请求包头：
方法           资源路径        协议及
  |     (URI，统一资源定位符)   其版本
  |           |                |
GET /user/project/main.html HTTP/1.1
  键         值
  |          |
Host: www.tmooc.cn\r\n
Accept: text/html\r\n
Connection: Keep-Alive\r\n
User-Agent: Mozilla/5.0\r\n
Referer: www.tmooc.cn\r\n
...
最后一行\r\n\r\n
```

代码：http.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
int main(int argc, char* argv[]) {
    // 解析命令行
    if (argc < 3) {
        fprintf(stderr, "用法：%s <主机IP地址> "
            "<主机域名> [<资源路径>]\n",
            argv[0]);
        return -1;
    }
    const char* ip = argv[1];
    const char* domain = argv[2];
    const char* path = argc < 4 ? "/" : argv[3];
    // 创建套接字
    int sockfd = socket(PF_INET, SOCK_STREAM, 0);
    if (sockfd == -1) {
        perror("socket");
        return -1;
    }
    // 服务器地址
    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(80);
    if (!inet_aton(ip, &addr.sin_addr)) {
        perror("inet_aton");
        return -1;
    }
    // 连接服务器
    if (connect(sockfd, (struct sockaddr*)&addr,
        sizeof(addr)) == -1) {
        perror("connect");
        return -1;
    }
    // 格式化请求
    char request[1024];
    sprintf(request,
        "GET %s HTTP/1.0\r\n"
        "Host: %s\r\n"
        "Accept: */*\r\n"
        "Connection: Close\r\n"
        "User-Agent: Mozilla/5.0\r\n"
        "Referer: %s\r\n\r\n",
        path, domain, domain);
    printf("%s", request);
    // 发送请求
    if (send(sockfd, request, strlen(request) *
        sizeof(request[0]), 0) == -1) {
        perror("send");
        return -1;
    }
    // 接收响应
    for (;;) {
        char respond[1024] = {};
        ssize_t rlen = recv(sockfd, respond,
            sizeof(respond) - sizeof(respond[0]),
            0);
        if (rlen == -1) {
            perror("recv");
            return -1;
        }
        if (!rlen)
            break;
        printf("%s", respond);
    }
    printf("\n");
    close(sockfd);
    return 0;
}
```

## 十四、多线程

### 14.1 什么是线程

源代码 -编译和链接-> 程序 -加载到内存中-> 进程
                                       |                                  |
                                    文件                             内存
                                                                        /   \
                                                                 代码 <- 执行
                                                                 数据 <- 处理
                                                                    |            | <- CPU
                                                                 静态      动态
                                                                    |            |
                                                                 资源      线程

线程就是进程的执行过程，即进程内部的控制序列，或者说是进程中的一个任务。
一个进程可以同时拥有多个线程，即同时被系统调度的多个执行路径，但至少要有一个主线程——main函数及被其调用的其它函数。
一个进程的所有线程都共享进程的代码区、数据区、BSS区、堆区、环境变量和命令行参数区、文件描述符表、信号处理函数、当前工作目录、用户和组的各种ID等。但是栈区不是共享的，一个进程的每个线程都拥有自己独立的栈区。

**线程调度：**
1)系统内核中专门负责线程调度的处理单元被称为调度器；
2)调度器将所有处于就绪状态(没有阻塞在任何系统调用上)的线程排成一个队列，即所谓就绪队列；
3)调度器从就绪队列中获取队首线程，为其分配一个时间片，并令处理器执行该线程，过了一段时间：
A.该线程的时间片耗尽，调度器立即终止该线程，并将其排到就绪队列的尾端，接着从队首获取下一个线程；
B.该线程的时间片未耗尽，但需阻塞于某系统调用，比如等待I/O或者睡眠。调度器会中止该线程，并将其从就绪队列中移至等待队列，直到其等待的条件满足后，再被移回就绪队列；
4)在低优先级线程执行期间，有高优先级线程就绪，后者会抢占前者的时间片；
5)若就绪队列为空，则系统内核进入空闲状态，直至其非空；
6)象Linux这样的多任务分时系统，基本的调度单位是线程；
7)为线程分配的时间片不宜过长，因为时间片太长会导致没有获得处理机的线程等候时间过久，降低系统运行的并行性，用户会感觉到明显的响应延迟；时间片也不宜过短，因为过短的时间片会增加在线程之间切换上下文的频率，降低系统的运行性能。

### 14.2 线程的基本特点

1)线程是进程中的独立实体，可以拥有自己的资源(如栈)，可以被独立标识——线程ID，同时也被作为基本调用单元，参数时间片的分配。
2)线程有不同的状态，如创建、运行、终止、暂停、恢复、取消等。
3)线程可以使用的大部分资源还是隶属于进程的，因此线程作为进程的一部分不能脱离进程独立存在。
4)一个进程可以同时执行多个线程，这些线程可以执行相同的代码，完成相同的任务，也可以执行不同的代码，完成不同的任务。
5)创建一个线程所花费的开销远小于创建进程的开销。线程也称为轻量级进程。因此在解决诸如并发问题等问题时，优先考虑多线程，其次才是多进程。
6)多线程的问题在于因为太多的资源被共享，极易导致冲突，为了解决冲突可能需要增加额外的开销，因此多进程仍然有它的优势。
进程，内存壁垒，通信。
线程，内存共享，同步。

### 14.3 POSIX线程

```c
#include <pthread.h>
-lpthread -> libpthread.so
```

### 14.4 创建线程

线程过程函数：在一个线程中被内核调用的函数，对该函数的调用过程就是线程的执行过程，从该函数中返回就意味着线程的结束。因此，main函数其实就是一个进程的主线程的线程过程函数。所有自创建的线程都必须有一个对应线程过程函数。

```c
void* 线程过程函数(void* 线程参数指针) {
    线程的执行过程
}
int pthread_create(pthread_t* tid,
    const pthread_attr_t* attr,              //pthread_self()获取线程TID
    void* (*start_routine)(void*),
    void* arg);
成功返回0，失败返回错误码。
tid - 输出线程标识(TID)。
attr - 线程属性，NULL表示缺省属性。
start_routine - 线程过程函数指针    
arg - 线程参数指针
pthread_create
    ->创建一个新线程
        ->调用线程过程函数(start_routine)
            并传入线程参数指针(arg)
```

被创建的子线程和创建该子线程的父线程是并行的关系，其调度顺序无法预知，因此当pthread_create函数返回时子线程执行的位置无从确定，其线程过程函数可能尚未被调用，也可能正在执行，甚至可能已经返回。传递给线程的参数对象，一定要在线程过程函数不再使用它的情况下才能被释放。

**注意：**不要用perror打印错误了，因为它的错误码不放在errnum里。

代码：create.c

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <pthread.h>
void* thread_proc(void* arg) {
    printf("%lu线程：%s\n",
        pthread_self(), (char*)arg);
    return NULL;
}
int main(void) {
    pthread_t tid;
    int error = pthread_create(&tid, NULL,
        thread_proc, "我是子线程！");
    if (error) {
        fprintf(stderr, "pthread_create: %s\n",
            strerror(error));
        return -1;
    }
    printf("%lu线程：我是主线程，"
        "创建%lu线程。\n", pthread_self(), tid);
    sleep(1);
    return 0;
}
```

主线程和通过pthread_create函数创建的多个子线程，在时间上“同时”运行，如果不附加任何同步条件，则它们每一个执行步骤的先后顺序完全无法预知，这就叫做自由并发。
代码：concur.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <pthread.h>
void* thread_proc(void* arg) {
    for (;;) {
        printf("%d", (int)arg);
        usleep((rand() % 100) * 1000);
    }
    return NULL;
}
int main(void) {
    srand(time(NULL));
    setbuf(stdout, NULL);
    pthread_t tid;
    for (int i = 0; i < 5; ++i)
        pthread_create(&tid, NULL, thread_proc,
            (void*)(i+1));
    getchar();
    return 0;
}
```

为了让线程过程函数的实现更加灵活，可以通过线程参数传递特定的信息，帮助线程过程函数执行不同的任务。
代码：arg.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>
#include <unistd.h>
#include <pthread.h>
#define PI 3.14159
void* thread_area(void* arg) {
    double r = *(double*)arg;
    *(double*)arg = PI * r * r;
    return NULL;
}
struct Pyth {
    double a, b, c;
};
void* thread_pyth(void* arg) {
    struct Pyth* pyth = (struct Pyth*)arg;
    pyth->c = sqrt((pyth->a *
        pyth->a + pyth->b * pyth->b));
    return NULL;
}
void* thread_aver(void* arg) {
    double* d = (double*)arg;
    d[2] = (d[0] + d[1]) / 2;
    return NULL;
}
void* thread_show(void* arg) {
    printf("%d\n", *(int*)arg);
    return NULL;
}
int main(void) {
    pthread_t tid;
    double r = 10;
    pthread_create(&tid, NULL, thread_area, &r);
    usleep(100000);
    printf("%g\n", r);
    struct Pyth pyth = {3, 4};
    pthread_create(&tid, NULL, thread_pyth,
        &pyth);
    usleep(100000);
    printf("%g\n", pyth.c);
    double d[3] = {123, 456};
    pthread_create(&tid, NULL, thread_aver,
        d);
    usleep(100000);
    printf("%g\n", d[2]);
    int* n = malloc(sizeof(int));
    *n = 1234;
    pthread_create(&tid, NULL, thread_show,
        n);
    usleep(100000);
    free(n);
    return 0;
}
```

### 14.5 汇合线程

```c
       创建点       汇合点
--------+---------+-------> 主线程
        \________/ 子线程
int phread_join(pthread_t tid, void** retval);
成功返回0，失败返回错误码。
tid - 线程标识
retval - 线程退出码
当调用pthread_join函数时：
tid线程已经终止，立刻返回，返回线程退出码。
tid线程尚未终止，阻塞等待，直到被汇合线程终止。
void* 线程过程函数(void* 线程参数指针) {    //代码不调这个函数是内核调，所以返回到内核去了   
    线程的执行过程
    return p;
}
内核
  p ---------------------
                      v
void* q; // retval->q[ ]
phread_join(..., &q);
等待子线程终止        \
清理线程的资源            这个函数有这三个功能，如果不调用它可能会有线程僵尸占用资源   
获得线程过程函数的返回值 /
```

代码：join.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>
#include <unistd.h>
#include <pthread.h>
#define PI 3.14159
void* thread_area(void* arg) {
    double r = *(double*)arg;
    double* s = malloc(sizeof(double));
    *s = PI * r * r;
    return s;
}
int main(void) {
    pthread_t tid;
    double r = 10;
    pthread_create(&tid, NULL, thread_area, &r);
    double* s;
    pthread_join(tid, (void**)&s);
    printf("%g\n", *s);
    free(s);
    return 0;
}
```

### 14.6 分离线程

在有些时候作为子线程的创建者，父线程可能并不关心子线程何时终止，同时父线程也不需要获得子线程的返回值。这种情况下，就可将子线程设置为分离线程，这样的线程一旦终止，它们的资源会被系统自动回收，而无需在其父线程中调用pthread_join函数。
int pthread_detach(pthread_t tid);
成功返回0，失败返回错误码。
tid - 线程标识

代码：detach.c

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <pthread.h>
void* thread_proc(void* arg) {
    for (int i = 0; i < 200; ++i) {
        putchar('-');
        usleep(50000);
    }
    return NULL;
}
int main(void) {
    setbuf(stdout, NULL);
    pthread_attr_t attr;
    pthread_attr_init(&attr);
    pthread_attr_setdetachstate(&attr,
        PTHREAD_CREATE_DETACHED);
    pthread_t tid;
    pthread_create(&tid, /*NULL*/&attr,
        thread_proc, NULL);
    pthread_attr_destroy(&attr);
    //pthread_detach(tid);
    int error = pthread_join(tid, NULL);
    if (error)
        fprintf(stderr, "pthread_join: %s\n",
            strerror(error));
    for (int i = 0; i < 200; ++i) {
        putchar('+');
        usleep(100000);
    }
    printf("\n");
    return 0;
}
```

```c
pthread_attr_t attr; //创建线程的第二个参数就是它，这个结构体很复杂，可以用                                           pthread_attr_setdetachstat函数改一些属性
pthread_attr_init(&attr); // 用缺省值初始化线程属性
pthread_attr_setdetachstat(&attr,
    PTHREAD_CREATE_DETACHED); //PTHREAD_CREATE_DETACHED,这个宏表示分离线程
pthread_create(..., &attr, ...);
pthread_attr_destroy(&attr);

适用于运行特别快的那些线程，因为如果使用pthread_detach,有可能会出现还没来得及分离，线程就结束的情况。
```

### 14.7 线程ID

```c
pthread_t tid;
pthread_create(&tid, ...);
                               |
                   输出子线程的TID
pthread_self(); -> 返回调用线程的TID
if (tid1 == tid2) // 兼容性不好
    ...
int pthread_equal(pthread_t tid1, pthread_t tid2);
两个TID相等返回非零，否则返回0。
if (pthread_equal(tid1, tid2)) // 兼容性好
    ...
    
有些系统上Tid不是个整数，而是一个结构体，如果是结构体的话就不能用"=="来判定了   
```

代码：equal.c

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <syscall.h>
#include <pthread.h>
pthread_t g_main; // 主线程的TID
void foo(void) {
    if (pthread_equal(pthread_self(), g_main))
        printf("foo函数在主线程中被调用了。\n");
    else
        printf("foo函数在子线程中被调用了。\n");
}
void* thread_proc(void* arg) {
    printf("系统内核的子线程TID：%ld\n",
        syscall(SYS_gettid));
    foo();
    return NULL;
}
int main(void) {
    g_main = pthread_self();
    printf("POSIX库的主线程TID：%lu\n", g_main);
    printf("系统内核的主线程TID：%ld\n",
        syscall(SYS_gettid));
    printf("进程的PID：%d\n", getpid());
    foo();
    pthread_t tid;
    pthread_create(&tid, NULL, thread_proc,
        NULL);
    pthread_join(tid, NULL);
    return 0;
}
```

通过pthread_self函数返回的线程ID和pthread_create函数输出的线程ID一样都是由PTHREAD库内部维护的虚拟(伪)线程ID，可用于其它需要提供线程ID的PTHREAD函数。系统内核维护的真实线程ID，可通过syscall(SYS_gettid)获得：
#include <unistd.h> // 声明syscall函数
#include <syscall.h> // 定义SYS_gettid宏
在Linux系统中，一个进程的PID实际上就是其主线程的TID。

### 14.8 终止线程(自己)

1)从线程过程函数中返回，执行该过程函数的线程即终止。其返回值可通过pthread_join函数的第二参数输出给调用者。
2)在线程过程函数及被其调用的任何函数中都可以调用pthread_exit函数终止当前线程：
void pthread_exit(void* retval);
该函数的参数retval就相当于线程过程函数的返回值，同样可被pthread_join的第二个参数输出给调用者。

代码：exit.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <math.h>
#include <unistd.h>
#include <pthread.h>
#define PI 3.14159
void calc_area(double r) {
    double* s = malloc(sizeof(double));
    *s = PI * r * r;
    pthread_exit(s);
}
void* thread_area(void* arg) {
    printf("调用calc_area函数...\n");
    calc_area(*(double*)arg);
    printf("从calc_area函数返回。\n");
    return NULL;
}
int main(void) {
    pthread_t tid;
    double r = 10;
    pthread_create(&tid, NULL, thread_area, &r);
    //pthread_exit(NULL);
    double* s;
    pthread_join(tid, (void**)&s);
    printf("%g\n", *s);
    free(s);
    return 0;
}
```

**注意：**在子线程中调用pthread_exit，只会终止调用线程自己，对其其它兄弟线程和主线程没有影响。但如果在主线程中调用pthread_exit，被终止的将是整个进程及其所包含的全部线程。

### 14.9 取消(其它)线程

```c
int pthread_cancel(pthread_t tid);
成功返回0，失败返回错误码。
tid - 被取消线程的TID。
该函数只是向特定线程发出取消请求，并不等待其终止运行。缺省情况下，线程在收到取消请求以后，并不会立即终止，而死仍继续运行，直到达到某个取消点。在取消点处，线程检查其自身是否已被取消，若是则立即终止。取消点通常出现在一些特定的系统调用中。
设置调用线程的取消状态为接受或忽略取消请求：
int pthread_setcancelstate(int state, int* oldstate);
state - 取消状态，可取以下值：
PTHREAD_CANCEL_ENABLE - 接受取消请求(缺省)
PTHREAD_CANCEL_DISABLE - 忽略取消请求
old_state - 输出原取消状态，可取NULL。
设置调用线程的取消类型为延迟取消或立即取消：
int pthread_setcanceltype(int type, int* oldtype);
type - 取消类型，可取以下值：
PTHREAD_CANCEL_DEFERRED - 延迟取消(缺省)，接到取消请求，如果不是忽略的话，继续运行一段时间，直到执行取消点时再终止。
PTHREAD_CANEL_ASYNCHRONOUS - 立即取消，接到取消请求，如果不是忽略的话，立刻终止运行。
old_type - 输出原取消类型，可取NULL。
```

代码：cancel.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <pthread.h>
void elapse(void) {
    for (unsigned int i = 0; i < 800000000; ++i);
}
void* thread_proc(void* arg) {
    /*
    // 禁止接受取消请求
    pthread_setcancelstate(
        PTHREAD_CANCEL_DISABLE, NULL);
    */
    // 取消类型立即取消
    pthread_setcanceltype(
        PTHREAD_CANCEL_ASYNCHRONOUS, NULL);
    for (;;) {
        printf("线程：子在川上曰，"
            "逝者如斯夫。\n");
        elapse();
    }
    return NULL;
}
int main(void) {
    setbuf(stdout, NULL);
    pthread_t tid;
    pthread_create(&tid, NULL, thread_proc,
        NULL);
    getchar();
    printf("向线程发送取消请求...\n");
    pthread_cancel(tid);
    printf("等待线程终止...\n");
    pthread_join(tid, NULL);
    printf("线程已终止。\n");
    return 0;
}
```

### 14.10 线程冲突

g = 0
线程1                                   线程2
把内存(g=0)中的值读入寄存器(eax=0)
把寄存器(eax=0->1)中的值加1
把寄存器(eax=1)中的值存入内存(g=1)
                                  把内存(g=1)中的值读入寄存器(eax=1)
                                  把寄存器(eax=1->2)中的值加1
                                  把寄存器(eax=2)中的值存入内存(g=2)
线程1                                   线程2
把内存(g=0)中的值读入寄存器(eax=0)
                                  把内存(g=0)中的值读入寄存器(eax=0)
把寄存器(eax=0->1)中的值加1
                                  把寄存器(eax=0->1)中的值加1
把寄存器(eax=1)中的值存入内存(g=1)
                                  把寄存器(eax=1)中的值存入内存(g=1)
当两个或两个的线程同时以非原子化的方式访问同一个对象时，极有可能导致对象的最终状态不稳定，这就是所谓的线程冲突。解决冲突的基本原则就是敏感操作的原子化，即保证一个线程完成这组敏感操作以后，再允许另个线程执行类似的操作，位于与共享资源有关的操作代码，在任何时候都只允许一个线程执行。

代码：vie.c

```c
#include <stdio.h>
#include <string.h>
#include <pthread.h>
unsigned int g = 0;
void* thread_proc(void* arg) {
    for (unsigned int i = 0; i < 100000000; ++i)
        ++g;
    return NULL;
}
int main(void) {
    pthread_t t1, t2;
    pthread_create(&t1, NULL, thread_proc,
        NULL);
    pthread_create(&t2, NULL, thread_proc,
        NULL);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    printf("g = %u\n", g);
    return 0;
}
```

### 14.11 互斥锁

```c
静态初始化
pthread_mutex_t mutex =
    PTHREAD_MUTEX_INITIALIZER;
动态初始化
pthread_mutex_t mutex;
pthread_mutex_init(&mutex, NULL); // 分配内核资源
...
pthread_mutex_destroy(&mutex); // 释放内核资源
...
pthread_mutex_lock(&mutex); // 锁定互斥锁
任何时刻只会有一个线程对特定的互斥锁锁定成功，其它试图对其锁定的线程会在此函数中阻塞等待，直到该互斥锁的持有者线程将其解锁为止。
pthread_mutex_unlock(&mutex); // 解锁互斥锁
对特定互斥锁对象锁定成功的线程通过此函数将其解锁，那些阻塞于对该互斥锁对象试图锁定的线程中的一个会被唤醒，得到该互斥锁，并从pthread_mutex_lock函数中返回。
void* thread1(void* arg) {
    ...
    pthread_mutex_lock(&mutex);
    执行操作，访问共享对象
    pthread_mutex_unlock(&mutex);
    ...
}
void* thread2(void* arg) {
    ...
    pthread_mutex_lock(&mutex);
    执行操作，访问共享对象
    pthread_mutex_unlock(&mutex);
    ...
}
```

代码：mutex.c、mutex2.c

互斥锁在保证数据一致性和防止并发冲突的同时，也牺牲了多线程应用的并发性，因此在设计上，应尽量少地使用互斥锁，或者说只有在需要被互斥锁保护的操作过程中，使用它，而对于保护需求不明显的场合尽量不用。

```c
mutex.c

#include <stdio.h>
#include <string.h>
#include <pthread.h>
unsigned int g = 0;
//pthread_mutex_t m = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t m;
void* thread_proc(void* arg) {
    //pthread_mutex_lock(&m);
    for (unsigned int i = 0; i < 100000000; ++i){
        pthread_mutex_lock(&m);
        ++g;
        pthread_mutex_unlock(&m);
    }
    //pthread_mutex_unlock(&m);
    return NULL;
}
int main(void) {
    pthread_mutex_init(&m, NULL);
    pthread_t t1, t2;
    pthread_create(&t1, NULL, thread_proc,
        NULL);
    pthread_create(&t2, NULL, thread_proc,
        NULL);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    pthread_mutex_destroy(&m);
    printf("g = %u\n", g);
    return 0;
}

mutex2.c

#include <stdio.h>
#include <string.h>
#include <pthread.h>
int g = 0;
//pthread_mutex_t m = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t m;
void* thread_proc1(void* arg) {
    for (unsigned int i = 0; i < 10000000; ++i){
        pthread_mutex_lock(&m);
        ++g;
        pthread_mutex_unlock(&m);
    }
    return NULL;
}
void* thread_proc2(void* arg) {
    for (unsigned int i = 0; i < 10000000; ++i){
        pthread_mutex_lock(&m);
        --g;
        pthread_mutex_unlock(&m);
    }
    return NULL;
}
int main(void) {
    pthread_mutex_init(&m, NULL);
    pthread_t t1, t2;
    pthread_create(&t1, NULL, thread_proc1,
        NULL);
    pthread_create(&t2, NULL, thread_proc2,
        NULL);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    pthread_mutex_destroy(&m);
    printf("g = %d\n", g);
    return 0;
}
```

### 14.12 信号量

功能和用法与XSI/IPC对象中的信号量集非常类似，但是XSI/IPC对象中的信号量集只能用于进程，而这里的信号量既可以用于进程，也可以用于线程。
#include <semphore.h>

初始化信号量

```c
int sem_init(sem_t* sem, int pshared,
    unsigned int value);
成功返回0，失败返回-1。
sem - 信号量。
pshared - 用0和非0分别表示该信号量用于线程或是进程。
value - 信号量的初值，即初始空闲资源数。
当pshared参数取0，信号量仅用于一个进程中的多个线程，其本质就是一个普通的全局变量，但如果改参数取值为非0，则信号量可用于不同的进程，其存储位置在可为这些进程所访问的共享内存中。
```

销毁信号量

```c
int sem_destroy(sem_t* sem);
成功返回0，失败返回-1。
sem - 信号量。
```

等待信号量

```c
int sem_wait(sem_t* sem);
成功返回0，失败返回-1。
如果信号量的当前值大于0，则将其减1并立即返回0，表示获得资源。如果该信号量的当前值等于0，这就意味着已经没有空闲资源可供分配，调用该该函数的线程或进程将被阻塞，直到因资源被释放信号量的值够减1为止。
```

释放信号量

```c
int sem_post(sem_t* sem);
成功返回0，失败返回-1。
将信号量的值加1。那些正阻塞于针对该信号量的sem_wait函数调用中的一个线程或进程将被唤醒，并在对信号量减1之后从sem_wait函数中返回。
```

获取信号量的当前值

```c
int sem_getvalue(sem_t* sem, int* sval);
成功返回0，失败返回-1。
sem - 信号量
sval - 输出信号量的当前值，即当前可分配资源数
```

代码：sem.c

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <pthread.h>
#include <semaphore.h>
#define MAX_CONNS  5 // 最大连接数
#define MAX_USERS 50 // 最大用户数
sem_t s;
void* thread_user(void* arg) {
    pthread_t tid = pthread_self();
    int sval;
    sem_getvalue(&s, &sval);
    printf("%lu线程：等待数据连接"
        "(还剩%d个空闲连接)...\n", tid, sval);
    sem_wait(&s);
    sem_getvalue(&s, &sval);
    printf("%lu线程：获得数据连接"
        "(还剩%d个空闲连接)...\n", tid, sval);
    usleep(1000000);
    sem_post(&s);
    sem_getvalue(&s, &sval);
    printf("%lu线程：释放数据连接"
        "(还剩%d个空闲连接)...\n", tid, sval);
    return NULL;
}
int main(void) {
    pthread_t tids[MAX_USERS];
    sem_init(&s, 0, MAX_CONNS);
    for (int i = 0; i < sizeof(tids) / sizeof(
        tids[0]); ++i)
        pthread_create(&tids[i], NULL,
            thread_user, NULL);
    for (int i = 0; i < sizeof(tids) / sizeof(
        tids[0]); ++i)
        pthread_join(tids[i], NULL);
    sem_destroy(&s);
    return 0;
}
```

### 14.13 死锁

代码：dl.c

```c
#include <stdio.h>
#include <string.h>
#include <unistd.h>
#include <pthread.h>
pthread_mutex_t a = PTHREAD_MUTEX_INITIALIZER;
pthread_mutex_t b = PTHREAD_MUTEX_INITIALIZER;
void* thread_proc1(void* arg) {
    printf("线程1：加锁a...\n");
    pthread_mutex_lock(&a);
    printf("线程1：加锁a成功！\n");
    sleep(1);
    printf("线程1：加锁b...\n");
    pthread_mutex_lock(&b);
    printf("线程1：加锁b成功！\n");
    // ...
    pthread_mutex_unlock(&b);
    printf("线程1：解锁b。\n");
    pthread_mutex_unlock(&a);
    printf("线程1：解锁a。\n");
    return NULL;
}
void* thread_proc2(void* arg) {
    printf("线程2：加锁b...\n");
    pthread_mutex_lock(&b);
    printf("线程2：加锁b成功！\n");
    sleep(1);
    printf("线程2：加锁a...\n");
    pthread_mutex_lock(&a);
    printf("线程2：加锁a成功！\n");
    // ...
    pthread_mutex_unlock(&a);
    printf("线程2：解锁a。\n");
    pthread_mutex_unlock(&b);
    printf("线程2：解锁b。\n");
    return NULL;
}
int main(void) {
    pthread_t t1, t2;
    pthread_create(&t1, NULL, thread_proc1,
        NULL);
    pthread_create(&t2, NULL, thread_proc2,
        NULL);
    pthread_join(t1, NULL);
    pthread_join(t2, NULL);
    printf("完成！\n");
    return 0;
}
```

死锁的四个必要条件：
1)独占排它：线程以独占的方式使用其所获得的资源，即在一个段时间内不允许其它线程使用该资源。这段时间内，任何试图请求该资源的线程只能在阻塞中等待，直到资源被其拥有者主动释放。
2)请求保持：线程已经拥有了至少一份资源，但又试图获取已被其它线程拥有的资源，因此只能在阻塞中等待，同时对自己已经获取的资源又坚守不放。
3)不可剥夺：线程已经获得的资源，在其未被使用完之前，不可被强制剥夺，而只能由其拥有者自己释放。
4)循环等待：线程集合{T0,T1,Tn}中，T0等待T1占有的资源，T1等待T2占有的资源，...，Tn等待T0占有的资源，形成环路。

### 14.14 条件变量

```c
线程1
    ...
    if (后续操作所依赖的条件不满足)
        睡眠于条件变量;
    后续操作
    ...
线程2
    ...
    创建出后续操作所依赖的条件
    唤醒在条件变量中睡眠的线程
    ...
静态初始化
pthread_cond_t cond = PTHREAD_COND_INITIALIZER;
动态初始化
pthread_cond_init(&cond, NULL);
...
pthread_cond_destroy(&cond);
等待条件变量，即在条件变量中睡眠
int pthread_cond_wait(pthread_cond_t* cond,
    pthread_mutex_t* mutex);
当调用线程在cond条件变量中睡眠期间mutex互斥锁会被解锁，直到该线程从条件变量中苏醒，即从该函数中返回再重新拥有该互斥锁。
int pthread_cond_signal(pthread_cond_t* cond);
唤醒在条件变量cond中睡眠的第一个线程，即条件等待队列的队首线程，待其重新获得互斥锁以后从pthread_cond_wait函数中返回。
int pthread_cond_broadcast(pthread_cond_t* cond);
唤醒在条件变量cond中睡眠的所有线程，只有重新获得互斥锁的线程会从pthread_cond_wait函数中返回。
生产者——消费者模型：
硬件/网络->生产者线程-数据->消费者线程
          \__________________/
                  刚性耦合
硬件/网络->生产者线程-数据->缓冲区-数据->消费者线程
          \________________________________/
                  柔性耦合
理想缓冲区：永远不满且不空
实际缓冲区：满不能放入——撑死，空不能提取——饿死。
生产者线程：
    if (缓冲区满)
        睡入非满条件变量，释放缓冲区锁
        被唤醒，离开条件等候队列，重新获得缓冲区锁
    生产->非空
    唤醒在非空条件变量中睡眠的消费者线程
消费者线程：
    if (缓冲区空)
        睡入非空条件变量，释放缓冲区锁
        被唤醒，离开条件等候队列，重新获得缓冲区锁
    消费->非满
    唤醒在非满条件变量中睡眠的生产者线程
```

代码：cond.c

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <unistd.h>
#include <pthread.h>
#define MAX_STOCK 5 // 仓库容量
char storage[MAX_STOCK]; // 仓库
int stock = 0; // 当前库存
pthread_mutex_t mutex =PTHREAD_MUTEX_INITIALIZER;
// 非满条件变量
pthread_cond_t full = PTHREAD_COND_INITIALIZER;
// 非空条件变量
pthread_cond_t empty = PTHREAD_COND_INITIALIZER;
// 显示库存
void show(char const* who, char const* op,
    char prod) {
    printf("%s：", who);
    for (int i = 0; i < stock; ++i)
        printf("%c", storage[i]);
    printf("%s%c\n", op, prod);
}
// 生产者线程
void* producer(void* arg) {
    char const* who = (char const*)arg;
    for (;;) {
        pthread_mutex_lock(&mutex);
        while (stock == MAX_STOCK) {
            printf("\033[;;32m%s：满仓！"
                "\033[0m\n", who);
            pthread_cond_wait(&full, &mutex);
        }
        char prod = 'A' + rand() % 26;
        show(who, "<-", prod);
        storage[stock++] = prod;
        //pthread_cond_signal(&empty);
        pthread_cond_broadcast(&empty);
        pthread_mutex_unlock(&mutex);
        usleep((rand() % 100) * 1000);
    }
    return NULL;
}
// 消费者线程
void* customer(void* arg) {
    char const* who = (char const*)arg;
    for (;;) {
        pthread_mutex_lock(&mutex);
        while (stock == 0) {
            printf("\033[;;31m%s：空仓！"
                "\033[0m\n", who);
            pthread_cond_wait(&empty, &mutex);
        }
        char prod = storage[--stock];
        show(who, "->", prod);
        //pthread_cond_signal(&full);
        pthread_cond_broadcast(&full);
        pthread_mutex_unlock(&mutex);
        usleep((rand() % 100) * 1000);
    }
    return NULL;
}
int main(void) {
    srand(time(NULL));
    pthread_attr_t attr;
    pthread_attr_init(&attr);
    pthread_attr_setdetachstate(&attr,
        PTHREAD_CREATE_DETACHED);
    pthread_t tid;
    pthread_create(&tid, &attr, producer,
        "生产者1");
    pthread_create(&tid, &attr, producer,
        "生产者2");
    pthread_create(&tid, &attr, customer,
        "消费者1");
    pthread_create(&tid, &attr, customer,
        "消费者2");
    getchar();
    return 0;
}
```

## 项目：基于HTTP协议的Web服务器(WWW服务器)

### 1.HTTP请求和响应

```c
1)HTTP请求
包头<空行>[包体]
方法 路径 协议
 |    |   |
 GET  / HTTP/1.1\r\n
Host: localhost:8000\r\n
User-Agent: Mozilla/5.0\r\n
Accept: text/html\r\n
Connection: keep-alive\r\n\r\n
[包体]
2)HTTP响应
包头<空行>[包体=页面内容(HTML语言+JavaScript脚本)]
   协议  响应码  响应信息(解释响应码的字符串)
    |      |      |
HTTP/1.1  200   OK\r\n
Server: Tarena WebServer 1.0\r\n
Date: Tue, 14 Jan 2020 01:39:00 GMT\r\n
Content-Type: text/html\r\n
Connection: keep-alive\r\n\r\n   (两个\r\n是为了和包体相隔开)
[包体=页面内容(HTML语言+JavaScript脚本)]
```

## 2.基于多线程的并发服务器模型

```c
创建流式(基于TCP协议)套接字(socket)
设置套接字选项(setsockopt)
准备地址并绑定(bind)
启动侦听(listen)
等待并接受连接请求(accept)<---+ <-连接请求- 浏览器
                           |
    创建子线程持有accept返回  |
    的连接套接字与浏览器通信----+
        接收并解析来自浏览器的HTTP请求<--+ <-浏览器
        找到浏览器请求的资源            |
        组织HTTP响应并发送给浏览器-------+  ->浏览器
```

## 3. 用多个功能模块组成完整的项目

mime模块：文件扩展名与内容类型映射表
    mime.h

```c
// mime.h - MIME类型
//
// 项目名称：WebServer (多线程版)
// 修改记录：
//
#ifndef _MIME_H
#define _MIME_H

static struct {
	char suffix[256];
	char type[256];
}	s_mime[] = {
	{"."       , "application/x-"                         },
	{".*"      , "application/octet-stream"               },
	{".001"    , "application/x-001"                      },
	{".301"    , "application/x-301"                      },
	{".323"    , "text/h323"                              },
	{".906"    , "application/x-906"                      },
	{".907"    , "drawing/907"                            },
	{".acp"    , "audio/x-mei-aac"                        },
	{".ai"     , "application/postscript"                 },
	{".aif"    , "audio/aiff"                             },
	{".aifc"   , "audio/aiff"                             },
	{".aiff"   , "audio/aiff"                             },
	{".a11"    , "application/x-a11"                      },
	{".anv"    , "application/x-anv"                      },
	{".apk"    , "application/vnd.android.package-archive"},
	{".asa"    , "text/asa"                               },
	{".asf"    , "video/x-ms-asf"                         },
	{".asp"    , "text/asp"                               },
	{".asx"    , "video/x-ms-asf"                         },
	{".au"     , "audio/basic"                            },
	{".avi"    , "video/avi"                              },
	{".awf"    , "application/vnd.adobe.workflow"         },
	{".biz"    , "text/xml"                               },
	{".bmp"    , "application/x-bmp"                      },
	{".bot"    , "application/x-bot"                      },
	{".c4t"    , "application/x-c4t"                      },
	{".c90"    , "application/x-c90"                      },
	{".cal"    , "application/x-cals"                     },
	{".cat"    , "application/vnd.ms-pki.seccat"          },
	{".cdf"    , "application/x-netcdf"                   },
	{".cdr"    , "application/x-cdr"                      },
	{".cel"    , "application/x-cel"                      },
	{".cer"    , "application/x-x509-ca-cert"             },
	{".cg4"    , "application/x-g4"                       },
	{".cgm"    , "application/x-cgm"                      },
	{".cit"    , "application/x-cit"                      },
	{".class"  , "java/*"                                 },
	{".cml"    , "text/xml"                               },
	{".cmp"    , "application/x-cmp"                      },
	{".cmx"    , "application/x-cmx"                      },
	{".cot"    , "application/x-cot"                      },
	{".crl"    , "application/pkix-crl"                   },
	{".crt"    , "application/x-x509-ca-cert"             },
	{".csi"    , "application/x-csi"                      },
	{".css"    , "text/css"                               },
	{".cut"    , "application/x-cut"                      },
	{".dbf"    , "application/x-dbf"                      },
	{".dbm"    , "application/x-dbm"                      },
	{".dbx"    , "application/x-dbx"                      },
	{".dcd"    , "text/xml"                               },
	{".dcx"    , "application/x-dcx"                      },
	{".der"    , "application/x-x509-ca-cert"             },
	{".dgn"    , "application/x-dgn"                      },
	{".dib"    , "application/x-dib"                      },
	{".dll"    , "application/x-msdownload"               },
	{".doc"    , "application/msword"                     },
	{".dot"    , "application/msword"                     },
	{".drw"    , "application/x-drw"                      },
	{".dtd"    , "text/xml"                               },
	{".dwf"    , "application/x-dwf"                      },
	{".dwg"    , "application/x-dwg"                      },
	{".dxb"    , "application/x-dxb"                      },
	{".dxf"    , "application/x-dxf"                      },
	{".edn"    , "application/vnd.adobe.edn"              },
	{".emf"    , "application/x-emf"                      },
	{".eml"    , "message/rfc822"                         },
	{".ent"    , "text/xml"                               },
	{".epi"    , "application/x-epi"                      },
	{".eps"    , "application/x-ps"                       },
	{".eps"    , "application/postscript"                 },
	{".etd"    , "application/x-ebx"                      },
	{".exe"    , "application/x-msdownload"               },
	{".fax"    , "image/fax"                              },
	{".fdf"    , "application/vnd.fdf"                    },
	{".fif"    , "application/fractals"                   },
	{".fo"     , "text/xml"                               },
	{".frm"    , "application/x-frm"                      },
	{".g4"     , "application/x-g4"                       },
	{".gbr"    , "application/x-gbr"                      },
	{".gif"    , "image/gif"                              },
	{".gl2"    , "application/x-gl2"                      },
	{".gp4"    , "application/x-gp4"                      },
	{".hgl"    , "application/x-hgl"                      },
	{".hmr"    , "application/x-hmr"                      },
	{".hpg"    , "application/x-hpgl"                     },
	{".hpl"    , "application/x-hpl"                      },
	{".hqx"    , "application/mac-binhex40"               },
	{".hrf"    , "application/x-hrf"                      },
	{".hta"    , "application/hta"                        },
	{".htc"    , "text/x-component"                       },
	{".htm"    , "text/html"                              },
	{".html"   , "text/html"                              },
	{".htt"    , "text/webviewhtml"                       },
	{".htx"    , "text/html"                              },
	{".icb"    , "application/x-icb"                      },
	{".ico"    , "image/x-icon"                           },
	{".iff"    , "application/x-iff"                      },
	{".ig4"    , "application/x-g4"                       },
	{".igs"    , "application/x-igs"                      },
	{".iii"    , "application/x-iphone"                   },
	{".img"    , "application/x-img"                      },
	{".ins"    , "application/x-internet-signup"          },
	{".ipa"    , "application/vnd.iphone"                 },
	{".isp"    , "application/x-internet-signup"          },
	{".IVF"    , "video/x-ivf"                            },
	{".java"   , "java/*"                                 },
	{".jfif"   , "image/jpeg"                             },
	{".jpe"    , "image/jpeg"                             },
	{".jpeg"   , "image/jpeg"                             },
	{".jpg"    , "image/jpeg"                             },
	{".js"     , "application/x-javascript"               },
	{".jsp"    , "text/html"                              },
	{".la1"    , "audio/x-liquid-file"                    },
	{".lar"    , "application/x-laplayer-reg"             },
	{".latex"  , "application/x-latex"                    },
	{".lavs"   , "audio/x-liquid-secure"                  },
	{".lbm"    , "application/x-lbm"                      },
	{".lmsff"  , "audio/x-la-lms"                         },
	{".ls"     , "application/x-javascript"               },
	{".ltr"    , "application/x-ltr"                      },
	{".m1v"    , "video/x-mpeg"                           },
	{".m2v"    , "video/x-mpeg"                           },
	{".m3u"    , "audio/mpegurl"                          },
	{".m4e"    , "video/mpeg4"                            },
	{".mac"    , "application/x-mac"                      },
	{".man"    , "application/x-troff-man"                },
	{".math"   , "text/xml"                               },
	{".mdb"    , "application/msaccess"                   },
	{".mfp"    , "application/x-shockwave-flash"          },
	{".mht"    , "message/rfc822"                         },
	{".mhtml"  , "message/rfc822"                         },
	{".mi"     , "application/x-mi"                       },
	{".mid"    , "audio/mid"                              },
	{".midi"   , "audio/mid"                              },
	{".mil"    , "application/x-mil"                      },
	{".mml"    , "text/xml"                               },
	{".mnd"    , "audio/x-musicnet-download"              },
	{".mns"    , "audio/x-musicnet-stream"                },
	{".mocha"  , "application/x-javascript"               },
	{".movie"  , "video/x-sgi-movie"                      },
	{".mp1"    , "audio/mp1"                              },
	{".mp2"    , "audio/mp2"                              },
	{".mp2v"   , "video/mpeg"                             },
	{".mp3"    , "audio/mp3"                              },
	{".mp4"    , "video/mpeg4"                            },
	{".mpa"    , "video/x-mpg"                            },
	{".mpd"    , "application/vnd.ms-project"             },
	{".mpe"    , "video/x-mpeg"                           },
	{".mpeg"   , "video/mpg"                              },
	{".mpg"    , "video/mpg"                              },
	{".mpga"   , "audio/rn-mpeg"                          },
	{".mpp"    , "application/vnd.ms-project"             },
	{".mps"    , "video/x-mpeg"                           },
	{".mpt"    , "application/vnd.ms-project"             },
	{".mpv"    , "video/mpg"                              },
	{".mpv2"   , "video/mpeg"                             },
	{".mpw"    , "application/vnd.ms-project"             },
	{".mpx"    , "application/vnd.ms-project"             },
	{".mtx"    , "text/xml"                               },
	{".mxp"    , "application/x-mmxp"                     },
	{".net"    , "image/pnetvue"                          },
	{".nrf"    , "application/x-nrf"                      },
	{".nws"    , "message/rfc822"                         },
	{".odc"    , "text/x-ms-odc"                          },
	{".out"    , "application/x-out"                      },
	{".p10"    , "application/pkcs10"                     },
	{".p12"    , "application/x-pkcs12"                   },
	{".p7b"    , "application/x-pkcs7-certificates"       },
	{".p7c"    , "application/pkcs7-mime"                 },
	{".p7m"    , "application/pkcs7-mime"                 },
	{".p7r"    , "application/x-pkcs7-certreqresp"        },
	{".p7s"    , "application/pkcs7-signature"            },
	{".pc5"    , "application/x-pc5"                      },
	{".pci"    , "application/x-pci"                      },
	{".pcl"    , "application/x-pcl"                      },
	{".pcx"    , "application/x-pcx"                      },
	{".pdf"    , "application/pdf"                        },
	{".pdx"    , "application/vnd.adobe.pdx"              },
	{".pfx"    , "application/x-pkcs12"                   },
	{".pgl"    , "application/x-pgl"                      },
	{".pic"    , "application/x-pic"                      },
	{".pko"    , "application/vnd.ms-pki.pko"             },
	{".pl"     , "application/x-perl"                     },
	{".plg"    , "text/html"                              },
	{".pls"    , "audio/scpls"                            },
	{".plt"    , "application/x-plt"                      },
	{".png"    , "image/png"                              },
	{".pot"    , "application/vnd.ms-powerpoint"          },
	{".ppa"    , "application/vnd.ms-powerpoint"          },
	{".ppm"    , "application/x-ppm"                      },
	{".pps"    , "application/vnd.ms-powerpoint"          },
	{".ppt"    , "application/vnd.ms-powerpoint"          },
	{".ppt"    , "application/x-ppt"                      },
	{".pr"     , "application/x-pr"                       },
	{".prf"    , "application/pics-rules"                 },
	{".prn"    , "application/x-prn"                      },
	{".prt"    , "application/x-prt"                      },
	{".ps"     , "application/x-ps"                       },
	{".ps"     , "application/postscript"                 },
	{".ptn"    , "application/x-ptn"                      },
	{".pwz"    , "application/vnd.ms-powerpoint"          },
	{".r3t"    , "text/vnd.rn-realtext3d"                 },
	{".ra"     , "audio/vnd.rn-realaudio"                 },
	{".ram"    , "audio/x-pn-realaudio"                   },
	{".ras"    , "application/x-ras"                      },
	{".rat"    , "application/rat-file"                   },
	{".rdf"    , "text/xml"                               },
	{".rec"    , "application/vnd.rn-recording"           },
	{".red"    , "application/x-red"                      },
	{".rgb"    , "application/x-rgb"                      },
	{".rjs"    , "application/vnd.rn-realsystem-rjs"      },
	{".rjt"    , "application/vnd.rn-realsystem-rjt"      },
	{".rlc"    , "application/x-rlc"                      },
	{".rle"    , "application/x-rle"                      },
	{".rm"     , "application/vnd.rn-realmedia"           },
	{".rmf"    , "application/vnd.adobe.rmf"              },
	{".rmi"    , "audio/mid"                              },
	{".rmj"    , "application/vnd.rn-realsystem-rmj"      },
	{".rmm"    , "audio/x-pn-realaudio"                   },
	{".rmp"    , "application/vnd.rn-rn_music_package"    },
	{".rms"    , "application/vnd.rn-realmedia-secure"    },
	{".rmvb"   , "application/vnd.rn-realmedia-vbr"       },
	{".rmx"    , "application/vnd.rn-realsystem-rmx"      },
	{".rnx"    , "application/vnd.rn-realplayer"          },
	{".rp"     , "image/vnd.rn-realpix"                   },
	{".rpm"    , "audio/x-pn-realaudio-plugin"            },
	{".rsml"   , "application/vnd.rn-rsml"                },
	{".rt"     , "text/vnd.rn-realtext"                   },
	{".rtf"    , "application/msword"                     },
	{".rtf"    , "application/x-rtf"                      },
	{".rv"     , "video/vnd.rn-realvideo"                 },
	{".sam"    , "application/x-sam"                      },
	{".sat"    , "application/x-sat"                      },
	{".sdp"    , "application/sdp"                        },
	{".sdw"    , "application/x-sdw"                      },
	{".sis"    , "application/vnd.symbian.install"        },
	{".sisx"   , "application/vnd.symbian.install"        },
	{".sit"    , "application/x-stuffit"                  },
	{".slb"    , "application/x-slb"                      },
	{".sld"    , "application/x-sld"                      },
	{".slk"    , "drawing/x-slk"                          },
	{".smi"    , "application/smil"                       },
	{".smil"   , "application/smil"                       },
	{".smk"    , "application/x-smk"                      },
	{".snd"    , "audio/basic"                            },
	{".sol"    , "text/plain"                             },
	{".sor"    , "text/plain"                             },
	{".spc"    , "application/x-pkcs7-certificates"       },
	{".spl"    , "application/futuresplash"               },
	{".spp"    , "text/xml"                               },
	{".ssm"    , "application/streamingmedia"             },
	{".sst"    , "application/vnd.ms-pki.certstore"       },
	{".stl"    , "application/vnd.ms-pki.stl"             },
	{".stm"    , "text/html"                              },
	{".sty"    , "application/x-sty"                      },
	{".svg"    , "text/xml"                               },
	{".swf"    , "application/x-shockwave-flash"          },
	{".tdf"    , "application/x-tdf"                      },
	{".tg4"    , "application/x-tg4"                      },
	{".tga"    , "application/x-tga"                      },
	{".tif"    , "image/tiff"                             },
	{".tiff"   , "image/tiff"                             },
	{".tld"    , "text/xml"                               },
	{".top"    , "drawing/x-top"                          },
	{".torrent", "application/x-bittorrent"               },
	{".tsd"    , "text/xml"                               },
	{".ttf"    , "application/font-woff"                  },
	{".txt"    , "text/plain"                             },
	{".uin"    , "application/x-icq"                      },
	{".uls"    , "text/iuls"                              },
	{".vcf"    , "text/x-vcard"                           },
	{".vda"    , "application/x-vda"                      },
	{".vdx"    , "application/vnd.visio"                  },
	{".vml"    , "text/xml"                               },
	{".vpg"    , "application/x-vpeg005"                  },
	{".vsd"    , "application/vnd.visio"                  },
	{".vsd"    , "application/x-vsd"                      },
	{".vss"    , "application/vnd.visio"                  },
	{".vst"    , "application/vnd.visio"                  },
	{".vst"    , "application/x-vst"                      },
	{".vsw"    , "application/vnd.visio"                  },
	{".vsx"    , "application/vnd.visio"                  },
	{".vtx"    , "application/vnd.visio"                  },
	{".vxml"   , "text/xml"                               },
	{".wav"    , "audio/wav"                              },
	{".wax"    , "audio/x-ms-wax"                         },
	{".wb1"    , "application/x-wb1"                      },
	{".wb2"    , "application/x-wb2"                      },
	{".wb3"    , "application/x-wb3"                      },
	{".wbmp"   , "image/vnd.wap.wbmp"                     },
	{".wiz"    , "application/msword"                     },
	{".wk3"    , "application/x-wk3"                      },
	{".wk4"    , "application/x-wk4"                      },
	{".wkq"    , "application/x-wkq"                      },
	{".wks"    , "application/x-wks"                      },
	{".wm"     , "video/x-ms-wm"                          },
	{".wma"    , "audio/x-ms-wma"                         },
	{".wmd"    , "application/x-ms-wmd"                   },
	{".wmf"    , "application/x-wmf"                      },
	{".wml"    , "text/vnd.wap.wml"                       },
	{".wmv"    , "video/x-ms-wmv"                         },
	{".wmx"    , "video/x-ms-wmx"                         },
	{".wmz"    , "application/x-ms-wmz"                   },
	{".wp6"    , "application/x-wp6"                      },
	{".wpd"    , "application/x-wpd"                      },
	{".wpg"    , "application/x-wpg"                      },
	{".wpl"    , "application/vnd.ms-wpl"                 },
	{".wq1"    , "application/x-wq1"                      },
	{".wri"    , "application/x-wri"                      },
	{".wrk"    , "application/x-wrk"                      },
	{".wr1"    , "application/x-wr1"                      },
	{".ws"     , "application/x-ws"                       },
	{".ws2"    , "application/x-ws"                       },
	{".wsc"    , "text/scriptlet"                         },
	{".wsdl"   , "text/xml"                               },
	{".wvx"    , "video/x-ms-wvx"                         },
	{".xap"    , "application/x-silverlight-app"          },
	{".xdp"    , "application/vnd.adobe.xdp"              },
	{".xdr"    , "text/xml"                               },
	{".xfd"    , "application/vnd.adobe.xfd"              },
	{".xfdf"   , "application/vnd.adobe.xfdf"             },
	{".xhtml"  , "text/html"                              },
	{".xls"    , "application/vnd.ms-excel"               },
	{".xls"    , "application/x-xls"                      },
	{".xlw"    , "application/x-xlw"                      },
	{".xml"    , "text/xml"                               },
	{".xpl"    , "audio/scpls"                            },
	{".xq"     , "text/xml"                               },
	{".xql"    , "text/xml"                               },
	{".xquery" , "text/xml"                               },
	{".xsd"    , "text/xml"                               },
	{".xsl"    , "text/xml"                               },
	{".xslt"   , "text/xml"                               },
	{".xwd"    , "application/x-xwd"                      },
	{".x_b"    , "application/x-x_b"                      },
	{".x_t"    , "application/x-x_t"                      }
};

#endif // _MIME_H
```

http模块：实现HTTP协议
    http.h、http.c

```c
http.h

// 声明与HTTP协议有关的数据类型和函数
#ifndef _HTTP_H
#define _HTTP_H
#include <limits.h>
#include <sys/types.h>
// HTTP请求
typedef struct tag_HttpRequest {
    char method[32];         // 方法
    char path[PATH_MAX + 1]; // 路径
    char protocol[32];       // 协议
    char connection[32];     // 连接
}   HTTP_REQUEST;
// 解析请求
int parseRequest(char const* req,
    HTTP_REQUEST* hreq);
// HTTP响应
typedef struct tag_HttpRespond {
    char  protocol[32];   // 协议
    int   status;         // 状态
    char  desc[256];      // 描述
    char  type[32];       // 类型
    off_t length;         // 长度
    char  connection[32]; // 连接
}   HTTP_RESPOND;
// 构造响应头
void constructHead(HTTP_RESPOND const* hres,
    char* head);
#endif // _HTTP_H

http.c
    
// 定义与HTTP协议有关的函数
#include <unistd.h>
#include <sys/syscall.h>
#include <stdio.h>
#define __USE_GNU
#include <string.h>
#include <time.h>
#include "http.h"
// 解析请求
int parseRequest(char const* req,
    HTTP_REQUEST* hreq) {
    /*
     * GET / HTTP/1.1\r\n
     * Host: localhost:8000\r\n
     * User-Agent: Mozilla/5.0\r\n
     * Accept: text/html\r\n
     * Connection: keep-alive\r\n\r\n
     */
    sscanf(req, "%s%s%s", hreq->method,
        hreq->path, hreq->protocol);
    char* connection = strcasestr(
        req, "connection");
    if (connection)
        sscanf(connection, "%*s%s",
            hreq->connection);
    printf("%d.%ld> [%s][%s][%s][%s]\n",
        getpid(), syscall(SYS_gettid),
        hreq->method, hreq->path,
        hreq->protocol, hreq->connection);
    if (strcasecmp(hreq->method, "get")) {
        printf("%d.%ld> 无效方法\n",
            getpid(), syscall(SYS_gettid));
        return -1;
    }
    if (strcasecmp(hreq->protocol, "http/1.0") &&
        strcasecmp(hreq->protocol, "http/1.1")) {
        printf("%d.%ld> 无效协议\n",
            getpid(), syscall(SYS_gettid));
        return -1;
    }
    return 0;
}
// 构造响应头
void constructHead(HTTP_RESPOND const* hres,
    char* head) {
    /*
     * HTTP/1.1 200 OK\r\n
     * Server: Tarena WebServer 1.0\r\n
     * Date: Tue, 14 Jan 2020 01:39:00 GMT\r\n
     * Content-Type: text/html\r\n
     * Content-Length: 2048\r\n
     * Connection: keep-alive\r\n\r\n
     */
    char dateTime[32];
    time_t now = time(NULL);
    strftime(dateTime, sizeof(dateTime),
        "%a, %d %b %Y %T GMT", gmtime(&now));
    sprintf(head,
        "%s %d %s\r\n"
        "Server: Tarena WebServer 1.0\r\n"
        "Date: %s\r\n"
        "Content-Type: %s\r\n"
        "Content-Length: %ld\r\n"
        "Connecion: %s\r\n\r\n",
        hres->protocol, hres->status, hres->desc,
        dateTime, hres->type, hres->length,
        hres->connection);
}
```

套接字模块：实现网络通信
    socket.h、socket.c

```c
socket.h

// 声明网络通信函数
#ifndef _SOCKET_H
#define _SOCKET_H
// 初始化套接字
int initSocket(short port);
// 接受客户机连接
int acceptClient(void);
// 接收请求
char* recvRequest(int conn);
// 发送响应头
int sendHead(int conn, char const* head);
// 发送响应体
int sendBody(int conn, char const* path);
// 终结化套接字
void deinitSocket(void);
#endif // _SOCKET_H

socket.c
    
// 定义网络通信函数
#include <unistd.h>
#include <fcntl.h>
#include <sys/syscall.h>
#include <sys/socket.h>
#include <netinet/in.h>
#include <arpa/inet.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "socket.h"
static int s_sock = -1; // 侦听套接字
// 初始化套接字
int initSocket(short port) {
    printf("%d> 创建套接字\n", getpid());
    if ((s_sock = socket(PF_INET, SOCK_STREAM,
        0)) == -1) {
        perror("socket");
        return -1;
    }
    printf("%d> 设置套接字\n", getpid());
    int on = 1;
    if (setsockopt(s_sock, SOL_SOCKET,
        SO_REUSEADDR, &on, sizeof(on)) == -1) {
        perror("setsockopt");
        return -1;
    }
    printf("%d> 绑定端口号\n", getpid());
    struct sockaddr_in addr;
    addr.sin_family = AF_INET;
    addr.sin_port = htons(port);
    addr.sin_addr.s_addr = INADDR_ANY;
    if (bind(s_sock, (struct sockaddr*)&addr,
        sizeof(addr)) == -1) {
        perror("bind");
        return -1;
    }
    printf("%d> 侦听套接字\n", getpid());
    if (listen(s_sock, 1024) == -1) {
        perror("listen");
        return -1;
    }
    return 0;
}
// 接受客户机连接
int acceptClient(void) {
    printf("%d> 等待客户机连接\n", getpid());
    struct sockaddr_in addrcli = {};
    socklen_t addrlen = sizeof(addrcli);
    int conn = accept(s_sock,
        (struct sockaddr*)&addrcli, &addrlen);
    if (conn == -1) {
        perror("accept");
        return -1;
    }
    printf("%d> 已连接客户机%s:%hu\n", getpid(),
        inet_ntoa(addrcli.sin_addr),
        ntohs(addrcli.sin_port));
    return conn;
}
// 接收请求
char* recvRequest(int conn) {
    char* req = NULL;
    ssize_t len = 0;
    for (;;) {
        char buf[1024] = {};
        ssize_t rlen = recv(conn, buf,
            sizeof(buf) - sizeof(buf[0]), 0);
        if (rlen == -1) {
            perror("recv");
            free(req);
            return NULL;
        }
        if (!rlen) {
            printf("%d.%ld> 客户机关闭连接\n",
                getpid(), syscall(SYS_gettid));
            free(req);
            return NULL;
        }
        req = (char*)realloc(req,
            len + rlen + sizeof(buf[0]));
        memcpy(req + len, buf,
            rlen + sizeof(buf[0]));
        len += rlen;
        if (strstr(req, "\r\n\r\n"))
            break;
    }
    return req;
}
// 发送响应头
int sendHead(int conn, char const* head) {
    if (send(conn, head, strlen(head) * sizeof(
        head[0]), 0) == -1) {
        perror("send");
        return -1;
    }
    return 0;
}
// 发送响应体
int sendBody(int conn, char const* path) {
    int fd = open(path, O_RDONLY);
    if (fd == -1) {
        perror("open");
        return -1;
    }
    char buf[1024];
    ssize_t len;
    while ((len = read(fd, buf, sizeof(
        buf))) > 0)
        if (send(conn, buf, len, 0) == -1) {
            perror("send");
            return -1;
        }
    if (len == -1) {
        perror("read");
        return -1;
    }
    close(fd);
    return 0;
}
// 终结化套接字
void deinitSocket(void) {
    close(s_sock);
}
```

资源模块：查找文件资源
    resource.h、resource.c

```c
resource.h
    
// 声明资源管理函数
#ifndef _RESOURCE_H
#define _RESOURCE_H
// 搜索资源
int searchResource(char const* path);
// 识别类型
int indentifyType(char const* path, char* type);
#endif // _RESOURCE_H

resource.c

// 定义资源管理函数
#include <unistd.h>
#include <sys/syscall.h>
#include <stdio.h>
#include <string.h>
#include "resource.h"
#include "mime.h"
// 搜索资源
int searchResource(char const* path) {
    return access(path, R_OK);
}
// 识别类型
// path: /xxx/xxx/home.html
int indentifyType(char const* path, char* type) {
    char* suffix = strrchr(path, '.');
    if (!suffix) {
        printf("%d.%ld> 无法获取文件扩展名\n",
            getpid(), syscall(SYS_gettid));
        return -1;
    }
    for (size_t i = 0; i < sizeof(s_mime) /
        sizeof(s_mime[0]); ++i)
        if (!strcasecmp(suffix,
            s_mime[i].suffix)) {
            strcpy(type, s_mime[i].type);
            return 0;
        }
    printf("%d.%ld> 不可识别的资源类型：%s\n",
        getpid(), syscall(SYS_gettid), suffix);
    return -1;
}
```

客户机模块：实现处理客户机业务的子线程
    client.h、client.c

```c
client.h
    
// 声明客户机处理线程的数据类型和函数
#ifndef _CLIENT_H
#define _CLIENT_H
// 客户机处理线程参数结构
typedef struct tag_ClientArgs {
    char const* home; // 资源主目录
    int         conn; // 连接套接字
}   CA;
// 客户机处理线程过程函数
void* client(void* arg);
#endif // _CLIENT_H

client.c
    
// 定义客户机处理线程函数
#include <unistd.h>
#include <sys/syscall.h>
#include <sys/stat.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "client.h"
#include "socket.h"
#include "http.h"
#include "resource.h"
// 客户机处理线程过程函数
void* client(void* arg) {
    CA* ca = (CA*)arg;
    printf("%d.%ld> 客户机处理开始\n",
        getpid(), syscall(SYS_gettid));
    for (;;) {
        printf("%d.%ld> 接收请求\n",
            getpid(), syscall(SYS_gettid));
        // 接收请求
        char* req = recvRequest(ca->conn);
        if (!req)
            goto escape;
        printf("%d.%ld> 请求电文\n\n%s",
            getpid(), syscall(SYS_gettid), req);
        printf("%d.%ld> 解析请求\n",
            getpid(), syscall(SYS_gettid));
        // 解析请求
        HTTP_REQUEST hreq = {};
        if (parseRequest(req, &hreq) == -1) {
            free(req);
            goto escape;
        }
        free(req);
        // 资源路径
        char path[PATH_MAX + 1];
        strcpy(path, ca->home);
        if (path[strlen(path) - 1] == '/')
            path[strlen(path) - 1] = '\0';
        strcat(path, hreq.path);
        if (!strcmp(hreq.path, "/"))
            strcat(path, "index.html");
        printf("%d.%ld> 资源路径：%s\n",
            getpid(), syscall(SYS_gettid), path);
        // 搜索资源
        HTTP_RESPOND hres = {
            "HTTP/1.1", 200, "OK", "text/html"};
        if (searchResource(path) == -1) {
            hres.status = 404;
            strcpy(hres.desc, "Not Found");
            strcpy(path, "../home/404.html");
        }
        else
        // 识别类型
        if (indentifyType(path,
            hres.type) == -1) {
            hres.status = 404;
            strcpy(hres.desc, "Not Found");
            strcpy(path, "../home/404.html");
        }
        // 长度
        struct stat st;
        if (stat(path, &st) == -1) {
            perror("stat");
            goto escape;
        }
        hres.length = st.st_size;
        // 连接
        if (strlen(hreq.connection))
            strcpy(hres.connection,
                hreq.connection);
        else
        if (!strcasecmp(hreq.protocol,
            "HTTP/1.0"))
            strcpy(hres.connection,
                "close");
        else
            strcpy(hres.connection,
                "keep-alive");
        printf("%d.%ld> 构造响应\n",
            getpid(), syscall(SYS_gettid));
        // 构造响应头
        char head[1024];
        constructHead(&hres, head);
        printf("%d.%ld> 响应电文\n\n%s",
            getpid(), syscall(SYS_gettid), head);
        printf("%d.%ld> 发送响应\n",
            getpid(), syscall(SYS_gettid));
        // 发送响应头
        if (sendHead(ca->conn, head) == -1)
            goto escape;
        // 发送响应体
        if (sendBody(ca->conn, path) == -1)
            goto escape;
        if (!strcasecmp(hres.connection,
            "close"))
            break;
    }
escape:
    close(ca->conn);
    free(ca);
    printf("%d.%ld> 处理结束\n",
        getpid(), syscall(SYS_gettid));
    return NULL;
}
```

信号模块：初始化信号处理
    signals.h、signals.c

```c
signals.h
    
// 声明信号函数
#ifndef _SIGNALS_H
#define _SIGNALS_H
// 初始化信号
int initSignals(void);
#endif // _SIGNALS_H

signals.c

// 定义信号函数
#include <unistd.h>
#include <signal.h>
#include <stdio.h>
#include "signals.h"
// 初始化信号
int initSignals(void) {
    printf("%d> 忽略尽可能多的信号\n", getpid());
    for (int signum = 1; signum <= 64; ++signum)
        if (signum != SIGINT &&
            signum != SIGTERM)
            signal(signum, SIG_IGN);
    return 0;
}
```

服务器模块：实现服务器的主线程
    server.h、server.c

```c
server.h
    
// 声明服务器函数
#ifndef _SERVER_H
#define _SERVER_H
// 初始化服务器
int initServer(short port);
// 运行服务器
int runServer(char const* home);
// 终结化服务器
void deinitServer(void);
#endif // _SERVER_H

server.c
// 定义服务器函数
#include <pthread.h>
#include <sys/resource.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "server.h"
#include "signals.h"
#include "socket.h"
#include "client.h"
// 初始化最大文件描述符数
static int initMaxFiles(void) {
    // 资源限制结构
    struct rlimit rl;
    // 获取当前进程最大文件描述符数
    if (getrlimit(RLIMIT_NOFILE, &rl) == -1) {
        perror("getrlimit");
        return -1;
    }
    // 若当前进程最大文件描述符数未达极限
    if (rl.rlim_cur < rl.rlim_max) {
        // 将当前进程最大文件描述符数设为极限
        rl.rlim_cur = rl.rlim_max;
        // 设置当前进程最大文件描述符数
        if (setrlimit(RLIMIT_NOFILE, &rl) == -1){
            perror("setrlimit");
            return -1;
        }
    }
    return 0;
}
// 初始化服务器
int initServer(short port) {
    // 初始化最大文件描述符数
    if (initMaxFiles() == -1)
        return -1;
    // 初始化信号
    if (initSignals() == -1)
        return -1;
    // 初始化套接字
    if (initSocket(port) == -1)
        return -1;
    return 0;
}
// 运行服务器
int runServer(char const* home) {
    for (;;) {
        // 接受客户机连接
        int conn = acceptClient();
        if (conn == -1)
            return -1;
        // 创建客户机处理线程
        pthread_t tid;
        pthread_attr_t attr;
        pthread_attr_init(&attr);
        pthread_attr_setdetachstate(&attr,
            PTHREAD_CREATE_DETACHED);
        CA* ca = (CA*)malloc(sizeof(CA));
        ca->home = home;
        ca->conn = conn;
        int error = pthread_create(&tid, &attr,
            client, ca);
        pthread_attr_destroy(&attr);
        if (error) {
            printf("pthread_create: %s\n",
                strerror(error));
            return -1;
        }
    }
    return 0;
}
// 终结化服务器
void deinitServer(void) {
    // 终结化套接字
    deinitSocket();
} 
```

主模块：实现main函数
    main.c

```c
// 定义main函数
#include <stdio.h>
#include <stdlib.h>
#include "server.h"
// WebServer 80 ../home
// WebServer 80
// WebServer
int main(int argc, char* argv[]) {
    // 初始化服务器
    if (initServer(argc < 2 ? 80 :
        atoi(argv[1])) == -1)
        return EXIT_FAILURE;
    // 运行服务器
    if (runServer(argc < 3 ? "../home" :
        argv[2]) == -1) {
        deinitServer();
        return EXIT_FAILURE;
    }
    // 终结化服务器
    deinitServer();
    return EXIT_SUCCESS;
}
```

构建脚本：makefile

```c
PROJ   = WebServer
OBJS   = main.o server.o signals.o socket.o \
         client.o http.o resource.o
CC     = gcc
LINK   = gcc
RM     = rm -rf
CFLAGS = -c -Wall -I.
LIBS   = -lpthread
$(PROJ): $(OBJS)
	$(LINK) $^ $(LIBS) -o $@
.c.o:
	$(CC) $(CFLAGS) $^
clean:
	$(RM) $(PROJ) $(OBJS) *.gch
```



