# 什么是 Calling Convention

当在一个程序中结合使用更复杂的子程序时，会出现许多复杂的问题。例如，如何将参数传递给子程序？子程序能否覆盖寄存器中的值，还是调用者期望寄存器的内容保持不变？子程序中的局部变量应该存储在哪里？如何从函数中返回结果？

为了让不同的程序员可以共享代码并开发供多个程序使用的库，并简化子程序的使用，程序员通常会采用一个通用的**调用约定**。调用约定就是一套规则，它可以明确回答上述问题，从而简化子程序的定义和使用。例如，遵循一套调用约定规则，程序员不需要检查子程序的定义就能确定如何将参数传递给该子程序。此外，遵循一套调用约定规则，高级语言编译器可以被设计为遵循这些规则，从而允许手工编写的汇编语言程序和高级语言程序相互调用。

实际上，即使对于单一的处理器指令集，也可能存在多种调用约定。在本课程中，我们将研究并使用其中最重要的一个约定：C语言调用约定。理解这个约定将使你能够编写可以从C和C++代码中安全调用的汇编语言子程序，同时也能使你在汇编语言代码中调用C库函数。

# C Calling Convention
C语言的调用约定主要依赖于硬件支持的栈的使用。要理解C语言的调用约定，你首先应该确保你完全理解`push`、`pop`、`call`和`ret`指令——这些将是大多数规则的基础。在这种调用约定中，子程序的参数通过栈传递，寄存器保存在栈上，子程序使用的局部变量也放置在栈中的内存中。事实上，这种以栈为中心的子程序实现方式并不仅限于C语言或x86架构。绝大多数在各种处理器上实现的高级过程式语言都使用了类似的调用约定。

调用约定分为两组规则。第一组规则由子程序的调用者使用，第二组规则由子程序的编写者（即“被调用者”）遵循。需要强调的是，未能遵守这些规则会迅速导致致命的程序错误；因此，在自己的子程序中实现这些调用约定时，应当非常谨慎。
> **push**: 把一个值save到栈上。栈是一个先进后出的内存。RBP(EBP)指向栈底， RSP(ESP)指向栈顶。栈是从高地址向低地址长。
> 例如： **push eax**  ; RSP减8(在32-bit，ESP 减4) 同时把 EAX 地址存在栈上。

> **pop**: 从栈顶删掉一个值， 并把这个值放到寄存器里或者某个地址上。
> 例如：**pop ebx**  ; 把指定值拷贝到EBX 寄存器, RSP加8（在32-bit, ESP 加4。

> **call**: 用来调用一个函数。它会保存下一条指令(返回指令)到栈上， 然后跳到函数的地址去执行（把函数地址放到RIP/EIP 寄存器中）。
> 例如: **call my_function**  ; 把下一条指令地址放到栈上(返回地址)。 跳到 'my_function' 的地址去执行。

> **ret**: 从函数调用中返回， 保存在栈上的返回地址被放到RIP/EIP 中去继续执行。此时返回地址是在栈顶的。64-bit 上是**retq**。地址在64-bit上是8 byte， 在32-bit上是4 byte。
> 例如：ret  ; 从栈顶取出返回地址， 从这个地址继续执行。


# 调用函数Caller 的rule
1.  在调用另一个函数之前， caller 需要把一些**保存着caller 使用data的寄存器的内容push到栈上**。 这些寄存器包括 r10, r11 和其他的一些保存参数的寄存器。这些内容往往是在调用另一个函数后还需要保持不变的。
2.  把被调用函数需要的**参数放到6个寄存器中**。如果函数参数超过了6个， 把其他的参数以相反的顺序压到栈上。
	> 六个寄存器依次是:
	
	> **rdi**: 第一个参数
	
	> **rsi**: 第二个参数
	
	> **rdx**: 第三个参数
	
	> **rcx**: 第四个参数
	
	> **r8**: 第五个参数
	
	> **r9**: 第六个参数
	

3.  **用 call 指令去调用被调用函数**。 **返回地址**（下一条指令地址）会被push到栈上， 然后跳到被调用函数的地址去执行。
4. 在从被调用函数返回后（紧跟着call指令的指令）， 需要**把超过6个的参数部分从栈上删除**。这样栈就恢复到了调用函数之前。
5. 被调用函数的返回值放在了**rax** 寄存器， caller 可以从这个寄存器或得返回值。

	> **rax 寄存器用途**:
	> 1. 通用寄存器
	> 2. 用在一些64-bit 指令中存储操作数。例如 mul, div 隐式的使用rax 存储数据。mul 中， rax 和另一个数进行乘法计算， 结果存在rax（低64 位） 和rdx（高64 位）
	> 3. 存储函数调用后的返回值。比如返回值是 指针 或者整形数。
	> 4. 
7. Caller 函数恢复 r10, 11 和任何传递参数的寄存器值， 并他们从栈里删除。

# 被调用函数的rule
1. 为**局部变量**分配寄存器或者栈空间。栈是从大地址向小地址增长， 所以RSP/ESP 会减小, 减小的数量有局部变量总共的空间决定。比如: 一个float 变量和一个long 变量 一共需要12 bytes， 所以汇编代码类似于:
	```asm
		sub rsp 12 // 在汇编代码里，有可能没有这一步， 隐式包含了这一步。
	```
2. 把**RBP/EBP** 入栈。同时把**RBX, R12~R15** 这些可能用来保存调用函数可能继续会用的数据的寄存器入栈(如果它们在被调用函数中被用到)。
3.  被调用函数结束，返回前， 把**返回值放到RAX寄存器**中。
4. 在被调用函数返回前， 以相反的顺序把 RBX, RBP, R12~R15 出栈填值。
5. 把**局部变量**出栈， RSP 会被加上局部变量总共的空间大小。
6. 被调用函数中最后一条指令是**retq**（ret in 32 bit）。这条指令会把返回地址从栈上取出， 然后跳到那条指令继续执行。


几乎每个函数的汇编代码里都会有这种代码:

```asm
push rbp
move rbp, rsp
...
pop rbp
```
其实这些code 不是必须得。 这是从32 bit calling convention 继承来的。 我们可以通过添加 **-fomit-frame-pointer** 告诉GCC 编译器不要生成这些执行。
> 打开这个flag之后， RBP(EBP) 将会被作为通用寄存器使用。 RSP/ESP 将会被用来访问局部变量， 访问函数参数。因为RBP/EBP 被作为通用寄存器使用， 效率会有所提高。缺点是debugging 将会变得更复杂。

# 实例
下面我们以一个最简单的实例， 从汇编level分析函数调用过程中的指令和栈的变化情况。
```cpp
 1 #include <iostream>
  2 using namespace std;
  3
  4 bool func1(int a, char *b)
  5 {
  6         char *x = b;
  7         int y = a +3;
  8         return true;
  9 }
 10
 11 bool func2(int a, char *b, int *c, long *d, char *e, int *f, int *g)
 12 {
 13         char *x = b;
 14         cout << x << endl;
 15         int y = a +3;
 16         func1(y, b);
 17         return true;
 18 }
 19
 20 int main()
 21 {
 22         int a = 3;
 23         char *str = "abc";
 24         func2(a, str, (int*)0x111, (long*)0x222, (char*)0x333, (int*)0x444, (int*)0x555);
 25         cout << a << endl;
 26         return 0;
 27 }
```

main() （调用函数）函数的汇编代码: 
```asm
>>>>>>>> Disassembling function: main()
0x00000000004008ff <+0>:        55      push   %rbp //保存调用函数的栈底寄存器（Frame Pointer）
0x0000000000400900 <+1>:        48 89 e5        mov    %rsp,%rbp //设置新的栈底寄存器
0x0000000000400903 <+4>:        48 83 ec 10     sub    $0x10,%rsp // 移动栈顶寄存器(Stack Pointer)用来放局部变量(int a, char*str). 
// 为什么是16 而不是12? 原因是stack alignment： stack 应该以
// 16 byte 对齐。
0x0000000000400907 <+8>:        c7 45 fc 03 00 00 00    movl   $0x3,-0x4(%rbp) // 保存 a=3
0x000000000040090e <+15>:       48 c7 45 f0 59 0a 40 00 movq   $0x400a59,-0x10(%rbp) //保存 char *b = "abc"
0x0000000000400916 <+23>:       48 8b 75 f0     mov    -0x10(%rbp),%rsi //开始位调用func2 做准备: 第二个参数放到rsi
0x000000000040091a <+27>:       8b 45 fc        mov    -0x4(%rbp),%eax // 第一个参数暂存在eax
0x000000000040091d <+30>:       48 83 ec 08     sub    $0x8,%rsp // 因为被调用函数有7个参数（超过6个）， 
// 所以最后一个参数要放到栈上， 把栈顶 寄存器往上移 8 bytes 用来存放第七个参数。
0x00000000004008ff      main    21      /home/kennyd/tmp/test.cpp
0x0000000000400900      main    21      /home/kennyd/tmp/test.cpp
0x0000000000400903      main    21      /home/kennyd/tmp/test.cpp
0x0000000000400907      main    22      /home/kennyd/tmp/test.cpp
0x000000000040090e      main    23      /home/kennyd/tmp/test.cpp
0x0000000000400916      main    24      /home/kennyd/tmp/test.cpp
0x000000000040091a      main    24      /home/kennyd/tmp/test.cpp
0x000000000040091d      main    24      /home/kennyd/tmp/test.cpp
===================================
0x0000000000400921 <+34>:       68 55 05 00 00  pushq  $0x555// 第七个参数入栈
0x0000000000400926 <+39>:       41 b9 44 04 00 00       mov    $0x444,%r9d // 第六个参数放到r9（低32 bit）
0x000000000040092c <+45>:       41 b8 33 03 00 00       mov    $0x333,%r8d // 第五个参数放到r8（低32 bit）
0x0000000000400932 <+51>:       b9 22 02 00 00  mov    $0x222,%ecx // 第四个参数放到ecx
0x0000000000400937 <+56>:       ba 11 01 00 00  mov    $0x111,%edx // 第三个参数放到edx
0x000000000040093c <+61>:       89 c7   mov    %eax,%edi // 第一个参数放到edi
0x000000000040093e <+63>:       e8 56 ff ff ff  callq  0x400899 <func2(int, char*, int*, long*, char*, int*, int*)> //调用func2
0x0000000000400943 <+68>:       48 83 c4 10     add    $0x10,%rsp //调用func2结束返回，把第七个参数和 char *b出栈(int a在后面还要用)。

0x0000000000400921      main    24      /home/kennyd/tmp/test.cpp
0x0000000000400926      main    24      /home/kennyd/tmp/test.cpp
0x000000000040092c      main    24      /home/kennyd/tmp/test.cpp
0x0000000000400932      main    24      /home/kennyd/tmp/test.cpp
0x0000000000400937      main    24      /home/kennyd/tmp/test.cpp
0x000000000040093c      main    24      /home/kennyd/tmp/test.cpp
0x000000000040093e      main    24      /home/kennyd/tmp/test.cpp
0x0000000000400943      main    24      /home/kennyd/tmp/test.cpp
===================================
0x0000000000400947 <+72>:       8b 45 fc        mov    -0x4(%rbp),%eax //把a 放到eax寄存器中
0x000000000040094a <+75>:       89 c6   mov    %eax,%esi // 把a的值放到 esi（第二个参数） 为调用 cout << a << endl做准备。
0x000000000040094c <+77>:       bf 60 10 60 00  mov    $0x601060,%edi // 第一个参数放到edi(the cout object)
0x0000000000400951 <+82>:       e8 1a fe ff ff  callq  0x400770 <_ZNSolsEi@plt> // 调用 operator<< (ostream& << int)
0x0000000000400956 <+87>:       be 20 07 40 00  mov    $0x400720,%esi // 把endl 的地址放到第二个参数
0x000000000040095b <+92>:       48 89 c7        mov    %rax,%rdi //把上一次调用发的返回值(cout object) 放到第一个参数
0x000000000040095e <+95>:       e8 ed fd ff ff  callq  0x400750 <_ZNSolsEPFRSoS_E@plt> // 调用operator<< (ostream& << endl)
0x0000000000400963 <+100>:      b8 00 00 00 00  mov    $0x0,%eax //返回值放到eax 寄存器

0x0000000000400947      main    25      /home/kennyd/tmp/test.cpp
0x000000000040094a      main    25      /home/kennyd/tmp/test.cpp
0x000000000040094c      main    25      /home/kennyd/tmp/test.cpp
0x0000000000400951      main    25      /home/kennyd/tmp/test.cpp
0x0000000000400956      main    25      /home/kennyd/tmp/test.cpp
0x000000000040095b      main    25      /home/kennyd/tmp/test.cpp
0x000000000040095e      main    25      /home/kennyd/tmp/test.cpp
0x0000000000400963      main    26      /home/kennyd/tmp/test.cpp
===================================
0x0000000000400968 <+105>:      c9      leaveq // 修改rsp和rbp 到上一个函数。
0x0000000000400969 <+106>:      c3      retq // 修改RIP寄存器返回。

0x0000000000400968      main    27      /home/kennyd/tmp/test.cpp
0x0000000000400969      main    27      /home/kennyd/tmp/test.cpp
===================================
```

func2 (被调用函数) 汇编代码:
```asm
>>>>>>>> Disassembling function: func2(int a, char*b, int*c, long*d, char*e, int*f, int*g)
0x0000000000400899 <+0>:        55      push   %rbp // 保存rbp 栈底寄存器（Frame Pointer）
0x000000000040089a <+1>:        48 89 e5        mov    %rsp,%rbp // 修改rbp 指向原来的栈顶(Stack Pointer) 寄存器
0x000000000040089d <+4>:        48 83 ec 40     sub    $0x40,%rsp // 为局部变量分配栈(char *x,  yint, a, *b, *c, *d, *e, *f, *g)
// 从高地址往地地址一次是 第一个局部变量，第二个局部变量..., 第一个参数， 第二个参数...
0x00000000004008a1 <+8>:        89 7d ec        mov    %edi,-0x14(%rbp) // 把第一个参数a 入栈。
0x00000000004008a4 <+11>:       48 89 75 e0     mov    %rsi,-0x20(%rbp) //把第二个参数b入栈
0x00000000004008a8 <+15>:       48 89 55 d8     mov    %rdx,-0x28(%rbp) // 把第三个参数c 入栈
0x00000000004008ac <+19>:       48 89 4d d0     mov    %rcx,-0x30(%rbp) // 把第四个参数d入栈
0x00000000004008b0 <+23>:       4c 89 45 c8     mov    %r8,-0x38(%rbp) // 把第五个参数e入栈

0x0000000000400899      func2   12      /home/kennyd/tmp/test.cpp
0x000000000040089a      func2   12      /home/kennyd/tmp/test.cpp
0x000000000040089d      func2   12      /home/kennyd/tmp/test.cpp
0x00000000004008a1      func2   12      /home/kennyd/tmp/test.cpp
0x00000000004008a4      func2   12      /home/kennyd/tmp/test.cpp
0x00000000004008a8      func2   12      /home/kennyd/tmp/test.cpp
0x00000000004008ac      func2   12      /home/kennyd/tmp/test.cpp
0x00000000004008b0      func2   12      /home/kennyd/tmp/test.cpp
===================================
0x00000000004008b4 <+27>:       4c 89 4d c0     mov    %r9,-0x40(%rbp) //把第六个参数f入栈
0x00000000004008b8 <+31>:       48 8b 45 e0     mov    -0x20(%rbp),%rax //把 第二个参数b放到eax中
0x00000000004008bc <+35>:       48 89 45 f8     mov    %rax,-0x8(%rbp) //把第二个参数b入栈（variable x）
0x00000000004008c0 <+39>:       48 8b 45 f8     mov    -0x8(%rbp),%rax // x 放到rax中
0x00000000004008c4 <+43>:       48 89 c6        mov    %rax,%rsi // 为调用 cout << x 做准备： 把第二个参数x 放到rsi中
0x00000000004008c7 <+46>:       bf 60 10 60 00  mov    $0x601060,%edi // 把第一个参数（cout object）放到rdi 寄存器
0x00000000004008cc <+51>:       e8 6f fe ff ff  callq  0x400740 <_ZStlsISt11char_traitsIcEERSt13basic_ostreamIcT_ES5_PKc@plt>
//调用cout << int*
0x00000000004008d1 <+56>:       be 20 07 40 00  mov    $0x400720,%esi //把第二个参数放到 rsi

0x00000000004008b4      func2   12      /home/kennyd/tmp/test.cpp
0x00000000004008b8      func2   13      /home/kennyd/tmp/test.cpp
0x00000000004008bc      func2   13      /home/kennyd/tmp/test.cpp
0x00000000004008c0      func2   14      /home/kennyd/tmp/test.cpp
0x00000000004008c4      func2   14      /home/kennyd/tmp/test.cpp
0x00000000004008c7      func2   14      /home/kennyd/tmp/test.cpp
0x00000000004008cc      func2   14      /home/kennyd/tmp/test.cpp
0x00000000004008d1      func2   14      /home/kennyd/tmp/test.cpp
===================================
0x00000000004008d6 <+61>:       48 89 c7        mov    %rax,%rdi //把调用返回值(cout) 放到第一个参数寄存器rdi中
0x00000000004008d9 <+64>:       e8 72 fe ff ff  callq  0x400750 <_ZNSolsEPFRSoS_E@plt> //调用 cout << endl
0x00000000004008de <+69>:       8b 45 ec        mov    -0x14(%rbp),%eax // 把a 放到eax 寄存器中
0x00000000004008e1 <+72>:       83 c0 03        add    $0x3,%eax // a+3
0x00000000004008e4 <+75>:       89 45 f4        mov    %eax,-0xc(%rbp) // 把a+3的结果赋给变量y
0x00000000004008e7 <+78>:       48 8b 55 e0     mov    -0x20(%rbp),%rdx //第二个参数b 放到rdx 寄存器 （调用func1的第三个参数）
0x00000000004008eb <+82>:       8b 45 f4        mov    -0xc(%rbp),%eax // 把y 放到eax 寄存器
0x00000000004008ee <+85>:       48 89 d6        mov    %rdx,%rsi // 把b 放到第二个参数寄存器rsi准备调用func1

0x00000000004008d6      func2   14      /home/kennyd/tmp/test.cpp
0x00000000004008d9      func2   14      /home/kennyd/tmp/test.cpp
0x00000000004008de      func2   15      /home/kennyd/tmp/test.cpp
0x00000000004008e1      func2   15      /home/kennyd/tmp/test.cpp
0x00000000004008e4      func2   15      /home/kennyd/tmp/test.cpp
0x00000000004008e7      func2   16      /home/kennyd/tmp/test.cpp
0x00000000004008eb      func2   16      /home/kennyd/tmp/test.cpp
0x00000000004008ee      func2   16      /home/kennyd/tmp/test.cpp
===================================
0x00000000004008f1 <+88>:       89 c7   mov    %eax,%edi // y放到第一个参数寄存器rdi 准备调用func1
0x00000000004008f3 <+90>:       e8 7e ff ff ff  callq  0x400876 <func1(int, char*)> //调用func1
0x00000000004008f8 <+95>:       b8 01 00 00 00  mov    $0x1,%eax // 把true 放到返回值寄存器eax中
0x00000000004008fd <+100>:      c9      leaveq // 修改rsp， rbp到调用函数
0x00000000004008fe <+101>:      c3      retq // 修改RIP 继续指令执行

0x00000000004008f1      func2   16      /home/kennyd/tmp/test.cpp
0x00000000004008f3      func2   16      /home/kennyd/tmp/test.cpp
0x00000000004008f8      func2   17      /home/kennyd/tmp/test.cpp
0x00000000004008fd      func2   18      /home/kennyd/tmp/test.cpp
0x00000000004008fe      func2   18      /home/kennyd/tmp/test.cpp
===================================
```
栈的变化情况:
![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/1.png)

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/2.png)

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/3.png)

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/4.png)
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTI3ODAyMjY1LC05OTcwMjE4NDksLTIxMT
EwMTgyMTIsLTE5MzQwMTUwMTYsLTM5OTcyMjI5NCwxMzEzNDg1
NjY0LC01ODA5MTg5NjEsLTIwNzU5NDcyNzQsLTQ0NjU3ODY4My
wtODA1MTMxNjMxLDk0ODg5NDc0LDExMzgxNDYyNTEsLTE5NjAy
NjUyNTMsODg1NTM5MzQ3LC0zNjI3NjY4NTIsMTc2NjI1MjQ0OC
wtMTkzNTM2NTI5OSwtMTMxMDU0ODYzLC0yOTA4NDk3OTcsLTI3
Nzc0NDA3N119
-->