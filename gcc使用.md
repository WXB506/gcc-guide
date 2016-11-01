###compile option
|后缀|类型|后缀|类型|
| --- | --- | --- | --- |
|.c|c源代码|.ii|预处理过的c++源代码|
|.C/.cc|c++源代码|.s|汇编语言的源代码|
|.h|头文件|.S|预编译的汇编语言源代码文件|
|.i|预处理过的c源代码|.o|编译后的目标文件|

| option | info | eg |
| --- | --- | --- |
| **-E** | 激活预处理 | gcc -E hello.c -o hello.i |
| **-S** | 生成汇编代码 | gcc -S hello.i -o hello.s |
| **-c** | 生成目标代码(可执行文件或 .a 静态链接文件或 .so 动态链接文件) | gcc -c hello.s -o hello.o | 
| **null** | 链接 | gcc hello.o -o hello |
| **-save-temps** | 将中间文件保存起来 .i .s .o | gcc -save-temps hello.c -o hello |
| **-pipe** | 借助GCC的管道功能来提高编译速度, 不产生中间文件, 但内存消耗较大 | gcc -pipe hello.c -o hello |
| **-i** | 相当于"#include" |
| **-l** | 链接库名lib*.a | -lfoo |
| **-I**(大写i) | 指定头文件路径 | -Idirname |
| **-L** | 指定库文件路径 | -Ldirname |
| **-Dmacro** | 定义指定的宏,使它能够通过源码中的#ifdef进行检验 |
| **-g3** | 获得有关调试程序的详细信息,它不能与-o选项联合使用 |
| **-O、-O2、-O3** | 将优化状态打开,该选项不能与-g选项联合使用 | 
| **-v** | 启动所有警报 |
| **-Wall** | 使GCC产生尽可能多的警告信息 |
| **-Werror** | 将所有的警告当成错误进行处理,这在使用自动编译工具（如Make等）时非常有用 |
| **-w** | 禁止所有的报警 |
####使用使用 -I 和 -L 引入外部头文件和库文件时路径的搜索次序
默认情况下，gcc在下面目录中搜索头文件：

1. /usr/local/include/
2. /usr/include/

在下面目录中搜索库：

1. /usr/local/lib/
2. /usr/lib/

指定后在-I下搜索头文件， 在-L下搜索库

相关的**环境变量**

C_INCLUDE_PATH（针对C的头文件）

CPP_INCLUDE_PATH（针对C++的头文件）

该目录将在命令行上用选项“-I”指定的任何目录之后，但在标准默认目录“/usr/local/include”和“/usr/include”之前被搜索

LIBRARY_PATH

该目录将在命令行上用选项“-L”指定的任何目录之后，但在标准默认目录“/usr/local/lib”和“/usr/lib”之前被搜索。

遵循标准Unix搜索路径的规范，搜索目录可以在环境变量中用冒号分隔的列表形式一起指定：

DIR1:DIR2:DIR3:...

这些目录被依次从左到右搜索。单个点“.”可以用来指示当前目录。

例如，下面的设置把安装软件包的当前目录，及在“foo”和“bar”目录下各自的“include”，“lib”目录放到默认的include和链接路径中去：

$ C_INCLUDE_PATH=.:/opt/foo/include:/bar/include

$ LIBRARY_PATH=.:/opt/foo/lib:/bar/lib

在命令行上可以重复使用“-I”和“-L”选项来指定多个搜索路径的目录。例如，下面的命令，

$ gcc -I. -I/opt/foo/include -I/bar/include -L. -L/opt/foo/lib -L/bar/lib .....

等同于上面在环境变量中设置的。

当环境变量和命令行选项被同时使用时，编译器按下面的次序搜索目录：

1. 从左到右搜索由命令行“-I”和“-L”指定的目录
2. 由环境变量，比如C_INCLUDE_PATH和LIBRARY_PATH指定的目录
3. 默认的系统目录

###debug option
  默认情况下,GCC在编译时不会将调试符号插入到生成的二进制代码中,因为这样会增加可执行文件的大小。如果需要在编译时生成调试符号信息,可以使用 GCC的-g或者-ggdb选项。GCC在产生调试符号时,同样采用了分级的思路,开发人员可以通过在-g选项后附加数字1、2或3来指定在代码中加入调 试信息的多少。默认的级别是2（-g2）,此时产生的调试信息包括扩展的符号表、行号、局部或外部变量信息。级别3（-g3）包含级别2中的所有调试信息,以及源代码中定义的宏。级别1（-g1）不包含局部变量和与行号有关的调试信息,因此只能够用于回溯跟踪和堆栈转储之用。回溯跟踪指的是监视程序在运行过程中的函数调用历史,堆栈转储则是一种以原始的十六进制格式保存程序执行环境的方法,两者都是经常用到的调试手段。

  GCC产生的调试符号具有普遍的适应性,可以被许多调试器加以利用,但如果使用的是GDB,那么还可以通过-ggdb选项在生成的二进制代码中包含GDB 专用的调试信息。这种做法的优点是可以方便GDB的调试工作,但缺点是可能导致其它调试器（如DBX）无法进行正常的调试。选项-ggdb能够接受的调试 级别和-g是完全一样的,它们对输出的调试符号有着相同的影响。

  需要注意的是,使用任何一个调试选项都会使最终生成的二进制文件的大小急剧增加,同时增加程序在执行时的开销,因此调试选项通常仅在软件的开发和调试阶段使用。

```c
//使用gdb调试
//crash.c
#include 
int main(void)
{
  int input =0;
  printf("Input an integer:");
  scanf("%d", input);
  printf("The integer you input is %d\n", input);
  return 0;
}
/*
编译并运行上述代码,会产生一个严重的段错误（Segmentation fault）如下：
$ gcc -g crash.c -o crash
$ ./crash
Input an integer:10
Segmentation fault

为了更快速地发现错误所在,可以使用GDB进行跟踪调试,方法如下：
$ gdb crash
GNU gdb Red Hat Linux (5.3post-0.20021129.18rh)
……
(gdb)
    当GDB提示符出现的时候,表明GDB已经做好准备进行调试了,现在可以通过run命令让程序开始在GDB的监控下运行：
(gdb) run
Starting program: /home/xiaowp/thesis/gcc/code/crash
Input an integer:10
Program received signal SIGSEGV, Segmentation fault.
0x4008576b in _IO_vfscanf_internal () from /lib/libc.so.6

仔细分析一下GDB给出的输出结果不难看出,程序是由于段错误而导致异常中止的,说明内存操作出了问题,具体发生问题的地方是在调用 _IO_vfscanf_internal ( )的时候。为了得到更加有价值的信息,可以使用GDB提供的回溯跟踪命令backtrace,执行结果如下：
(gdb) backtrace
#0  0x4008576b in _IO_vfscanf_internal () from /lib/libc.so.6
#1  0xbffff0c0 in ?? ()
#2  0x4008e0ba in scanf () from /lib/libc.so.6
#3  0x08048393 in main () at crash.c:11
#4  0x40042917 in __libc_start_main () from /lib/libc.so.6

跳过输出结果中的前面三行,从输出结果的第四行中不难看出,GDB已经将错误定位到crash.c中的第11行了。现在仔细检查一下：
(gdb) frame 3
#3  0x08048393 in main () at crash.c:11
11       scanf("%d", input);

使用GDB提供的frame命令可以定位到发生错误的代码段,该命令后面跟着的数值可以在backtrace命令输出结果中的行首找到。现在已经发现错误所在了,应该将
scanf("%d", input);
改为
scanf("%d", &input);
    完成后就可以退出GDB了,命令如下：
(gdb) quit
*/
```
GDB的功能远远不止如此,它还可以单步跟踪程序、检查内存变量和设置断点等。

调试时可能会需要用到编译器产生的中间结果,这时可以使用-save-temps选项,让GCC将预处理代码、汇编代码和目标代码都作为文件保存起来。如果想检查生成的代码是否能够通过手工调整的办法来提高执行性能,在编译过程中生成的中间文件将会很有帮助。

GCC支持的其它调试选项还包括-p和-pg,它们会将剖析（Profiling）信息加入到最终生成的二进制代码中。剖析信息对于找出程序的性能瓶颈很有帮助,是开发出高性能程序的有力工具。在编译时加入-p选项会在生成的代码中加入通用剖析工具（Prof）能够识别的统计信息,而 -pg选项则生成只有GNU剖析工具（Gprof）才能识别的统计信息。