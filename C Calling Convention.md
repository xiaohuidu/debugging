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

**main() （调用函数）函数的汇编代码:** 
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

**func2 (被调用函数) 汇编代码:**
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
**func1 汇编代码:**
```asm
>>>>>>>> Disassembling function: func1(int, char*)
0x0000000000400876 <+0>:        55      push   %rbp
0x0000000000400877 <+1>:        48 89 e5        mov    %rsp,%rbp
0x000000000040087a <+4>:        89 7d ec        mov    %edi,-0x14(%rbp)
0x000000000040087d <+7>:        48 89 75 e0     mov    %rsi,-0x20(%rbp)
0x0000000000400881 <+11>:       48 8b 45 e0     mov    -0x20(%rbp),%rax
0x0000000000400885 <+15>:       48 89 45 f8     mov    %rax,-0x8(%rbp)
0x0000000000400889 <+19>:       8b 45 ec        mov    -0x14(%rbp),%eax
0x000000000040088c <+22>:       83 c0 03        add    $0x3,%eax

0x0000000000400876      func1   5       /home/kennyd/tmp/test.cpp
0x0000000000400877      func1   5       /home/kennyd/tmp/test.cpp
0x000000000040087a      func1   5       /home/kennyd/tmp/test.cpp
0x000000000040087d      func1   5       /home/kennyd/tmp/test.cpp
0x0000000000400881      func1   6       /home/kennyd/tmp/test.cpp
0x0000000000400885      func1   6       /home/kennyd/tmp/test.cpp
0x0000000000400889      func1   7       /home/kennyd/tmp/test.cpp
0x000000000040088c      func1   7       /home/kennyd/tmp/test.cpp
===================================
0x000000000040088f <+25>:       89 45 f4        mov    %eax,-0xc(%rbp)
0x0000000000400892 <+28>:       b8 01 00 00 00  mov    $0x1,%eax
0x0000000000400897 <+33>:       5d      pop    %rbp
0x0000000000400898 <+34>:       c3      retq

0x000000000040088f      func1   7       /home/kennyd/tmp/test.cpp
0x0000000000400892      func1   8       /home/kennyd/tmp/test.cpp
0x0000000000400897      func1   9       /home/kennyd/tmp/test.cpp
0x0000000000400898      func1   9       /home/kennyd/tmp/test.cpp
===================================

```
**栈的变化情况:**

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/1.png)

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/2.png)

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/3.png)

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/4.png)

如果我们在第七行加断点， 查看 寄存器和栈情况:

```asm
(gdb) info registers
rax            0x400a59            4196953
rbx            0x0                 0
rcx            0xb40               2880
rdx            0x400a59            4196953
rsi            0x400a59            4196953
rdi            0x6                 6
rbp            0x7fffffffdc30      0x7fffffffdc30
rsp            0x7fffffffdc30      0x7fffffffdc30
r8             0x7ffff749b860      140737342191712
r9             0x7ffff7fe4f40      140737354026816
r10            0xa                 10
r11            0x246               582
r12            0x400790            4196240
r13            0x7fffffffdd80      140737488346496
r14            0x0                 0
r15            0x0                 0
rip            0x400889            0x400889 <func1(int, char*)+19>
eflags         0x206               [ PF IF ]
cs             0x33                51
ss             0x2b                43
ds             0x0                 0
es             0x0                 0
fs             0x0                 0
gs             0x0                 0
k0             0x0                 0
k1             0x0                 0
k2             0x0                 0
k3             0x0                 0
k4             0x0                 0
k5             0x0                 0
k6             0x0                 0
k7             0x0                 0
```

```asm
(gdb) x/100x $rsp
0x7fffffffdc30: 0xffffdc80      0x00007fff      0x004008f8      0x00000000 //1. 当前RBP is here(0x7fffffffdc30), 
// 调用函数(func2)RBP is 0x7fffffffdc80. 返回地址(0x004008f8)
0x7fffffffdc40: 0x00000444      0x00000000      0x00000333      0x00000000
0x7fffffffdc50: 0x00000222      0x00000000      0x00000111      0x00000000 // 6. 第三个参数0x111,第四个参数0x222...
0x7fffffffdc60: 0x00400a59      0x00000000      0x004009bb      0x00000003 // 5. func2 函数中, -0x14(%rbp) 存的是第一个参数 a(3).
// -0x20(%rbp) 存的是第二个参数 *str(0x00400a59)
0x7fffffffdc70: 0x00000002      0x00000006      0x00400a59      0x00000000
0x7fffffffdc80: 0xffffdcb0      0x00007fff      0x00400943      0x00000000 // 2. func2的RBP(0x7fffffffdc80),
// func2的调用函数main的RBP(0x7fffffffdcb0), 返回地址(0x00400943)
0x7fffffffdc90: 0x00000555      0x00000000      0x00400790      0x00000000
0x7fffffffdca0: 0x00400a59      0x00000000      0x00000000      0x00000003 // 4. main 函数中 -0x4(%rbp）存的是a的值（3）
// main 函数中-0x10(%rbp)存的是*str(0x00400a59)
0x7fffffffdcb0: 0x004009c0      0x00000000      0xf7114d85      0x00007fff // 3. main函数的RBP(0x7fffffffdcb0) 
0x7fffffffdcc0: 0xf7afb9a0      0x00007fff      0xffffdd88      0x00007fff
0x7fffffffdcd0: 0xf7afb960      0x00000001      0x004008ff      0x00000000
0x7fffffffdce0: 0x00000000      0x00000000      0x693f8295      0x3d190cbe
0x7fffffffdcf0: 0x00400790      0x00000000      0xffffdd80      0x00007fff
0x7fffffffdd00: 0x00000000      0x00000000      0x00000000      0x00000000
0x7fffffffdd10: 0xc33f8295      0xc2e6f3c1      0xe0cd8295      0xc2e6e21c
0x7fffffffdd20: 0x00000000      0x00007fff      0x00000000      0x00000000
0x7fffffffdd30: 0x00000000      0x00000000      0xf7dd701a      0x00007fff
0x7fffffffdd40: 0x00000000      0x00000000      0x00000000      0x00000000
0x7fffffffdd50: 0x00000000      0x00000000      0x00000000      0x00000000
0x7fffffffdd60: 0x00000000      0x00000000      0x004007be      0x00000000
0x7fffffffdd70: 0xffffdd78      0x00007fff      0xf7ffe100      0x00007fff
0x7fffffffdd80: 0x00000001      0x00000000      0xffffe09d      0x00007fff
0x7fffffffdd90: 0x00000000      0x00000000      0xffffe0b3      0x00007fff
0x7fffffffdda0: 0xffffe69e      0x00007fff      0xffffe6de      0x00007fff
0x7fffffffddb0: 0xffffe713      0x00007fff      0xffffe747      0x00007fff

```

这是代码实例:
Segv report:

```
164455.cngss-587f8b9bf4-hckcp.cngss_cngss-587f8b9bf4-hckcp.csv:ERROR,EN_USER,"Task IMS  Segmentation Violation (Signal=11, SIGRTMIN=34) Event=11
Report 1 of 7
Thread Restart 1 of 1
Single Thread Process Initialization 1 of 7
Multiple Thread Process Initialization 1 of 12
Perform Thread Restart
Function trace:
000000001196def7 0000000011a6d7ca 00000000108be59c 00000000108c47b0 00000000115fac90
00000000115fb352 0000000011606f5c 000000001156726c 
",exception,N_USER_OPER,xxxxxxx:32,,,0,,,,,R_SUCCESS,E_NA,,,,,,,,,2024-08-06T16:44:58.560Z,E_NON_TRUE_TO_SOURCE_CSV,"{""build_version"":""37.22.05.0000:8 ng"",""destination"":""master"",""file_name"":""OSinterface.c"",""line"":""2182"",""status"":""UNLOCK_ACTIVE"",""vm_ip"":""xxxxxxx30"",""log_attempts"":""1911"",""logs_sent"":""245""}"
164455.cngss-587f8b9bf4-hckcp.cngss_cngss-587f8b9bf4-hckcp.csv:ERROR,EN_USER,"Task IMS  Segmentation Violation (Signal=11, SIGRTMIN=34) Event=11
Report 2 of 7
Register Dump:
     R8= 0000000000000000        R9= 00007fd9b67f2f18       R10= 0000000000000001
    R11= 0000000000000000       R12= 0000000000000000       R13= 0000000000000008
    R14= 0000000000000000       R15= 4cfe657a00f4000c       RDI= 0000000000000000
    RSI= 0000000000000005       RBP= 00007fd9b67f33e0       RBX= 0000000000000008
    RDX= 00007fd9b67f2f52       RAX= 0000000000000001       RCX= 0000000000000000
    RSP= 00007fd9b67f32c0       RIP= 000000001196def7       EFL= 0000000000010202
 CSGSFS= 002b000000000033       ERR= 0000000000000006    TRAPNO= 000000000000000e
OLDMASK= 0000000000004000       CR2= 0000000000000000   
",exception,N_USER_OPER,xxxxxxx:32,,,0,,,,,R_SUCCESS,E_NA,,,,,,,,,2024-08-06T16:44:58.560Z,E_NON_TRUE_TO_SOURCE_CSV,"{""build_version"":""37.22.05.0000:8 ng"",""destination"":""master"",""file_name"":""OSinterface.c"",""line"":""2219"",""status"":""UNLOCK_ACTIVE"",""vm_ip"":""xxxxxxx30"",""log_attempts"":""1912"",""logs_sent"":""246""}"
164455.cngss-587f8b9bf4-hckcp.cngss_cngss-587f8b9bf4-hckcp.csv:ERROR,EN_USER,"Task IMS  Segmentation Violation (Signal=11, SIGRTMIN=34) Event=11
Report 3 of 7
Stack dump:
Stack pointer = 00007fd9b67f32c0
Stack dump:
<00007fd9b67f32c0> 6cb95f0b da7f0000 08000000 00000000
<00007fd9b67f32d0> 06000000 da7f0000 0c00f400 7a65fe4c
<00007fd9b67f32e0> 1b250800 00000000 0c00f400 7a65fe4c
<00007fd9b67f32f0> d0337fb6 d97f0500 1b250800 7a65fe4c
<00007fd9b67f3300> 01000000 00000000 01000000 06000000
<00007fd9b67f3310> 80337fb6 d97f0000 d2003710 00000000
<00007fd9b67f3320> 00000000 00000000 1b250800 ffff0000
<00007fd9b67f3330> 01000000 00000000 05000000 67413c93
<00007fd9b67f3340> 00000000 00000000 00000000 00000000
<00007fd9b67f3350> 01000000 00000000 0c00f400 7a65fe4c
<00007fd9b67f3360> d0337fb6 d97f0000 0c00f400 7a65fe4c
<00007fd9b67f3370> 5f000000 00000000 33000000 00000000
<00007fd9b67f3380> e0357fb6 d97f0000 193f3710 00000000
<00007fd9b67f3390> 0c00f400 7a65fe4c 00000000 00000000
<00007fd9b67f33a0> b0357fb6 d97f0000 cb7425dd db7f0000
<00007fd9b67f33b0> 0180adfb 3a61746c ccb95f0b da7f0000
<00007fd9b67f33c0> 6cb95f0b da7f0000 04000000 00000000
<00007fd9b67f33d0> 10000000 00000000 0c00f400 7a65fe4c
<00007fd9b67f33e0> e0357fb6 d97f0000 d2d7a611 00000000 // RBP:MediaPolicy_nk_red_data_rcvCheckpt  
<00007fd9b67f33f0> 00000000 00000000 00000000 00000000
<00007fd9b67f3400> 00000000 00000000 00000000 00000000
<00007fd9b67f3410> 00000000 00000000 33000000 00000000
<00007fd9b67f3420> 0c815f0b 00000000 08000000 d97f0000
<00007fd9b67f3430> d4b95f0b da7f0000 00000000 00000000
<00007fd9b67f3440> 0c845f0b da7f0000 b8a020e9 d97f0000
<00007fd9b67f3450> 2e000000 00000000 00000000 00000000
<00007fd9b67f3460> e0357fb6 d97f0000 5a5a6011 00000000
<00007fd9b67f3470> ffffffff d97f0000 b16bcee3 db7f0000
<00007fd9b67f3480> b0347fb6 d97f0000 40a45add db7f0000
<00007fd9b67f3490> 00000000 03000000 a04c0000 03000000
<00007fd9b67f34a0> 00000000 00000000 33000000 00000000
<00007fd9b67f34b0> e0357fb6 d97f0000 33000000 00000000
",exception,N_USER_OPER,xxxxxxx:32,,,0,,,,,R_SUCCESS,E_NA,,,,,,,,,2024-08-06T16:44:58.560Z,E_NON_TRUE_TO_SOURCE_CSV,"{""build_version"":""37.22.05.0000:8 ng"",""destination"":""master"",""file_name"":""OSinterface.c"",""line"":""2236"",""status"":""UNLOCK_ACTIVE"",""vm_ip"":""xxxxxxx30"",""log_attempts"":""1913"",""logs_sent"":""247""}"
164455.cngss-587f8b9bf4-hckcp.cngss_cngss-587f8b9bf4-hckcp.csv:ERROR,EN_USER,"Task IMS  Segmentation Violation (Signal=11, SIGRTMIN=34) Event=11
Report 4 of 7
Stack dump:
Stack pointer = 00007fd9b67f34c0
Stack dump:
<00007fd9b67f34c0> 6cb95f0b da7f0000 179623dd db7f0000
<00007fd9b67f34d0> 401d0000 00000000 18000000 30000000
<00007fd9b67f34e0> b0357fb6 d97f0000 f0347fb6 d97f0000
<00007fd9b67f34f0> 1b000000 13000000 a04cb614 00000000
<00007fd9b67f3500> 00000000 00000000 333128dd db7f0000
<00007fd9b67f3510> 00000000 00000000 80975a14 00000000
<00007fd9b67f3520> 09000000 00000000 404c6012 00000000
<00007fd9b67f3530> 10d820e9 d97f0000 08815f0b da7f0000
<00007fd9b67f3540> 09000000 00000000 2c545f0b da7f0000
<00007fd9b67f3550> 24000000 00000000 6c875f0b da7f0000
<00007fd9b67f3560> 24000000 00000000 f8a020e9 d97f0000
<00007fd9b67f3570> 14000000 00000000 b7f298a0 3344b586
<00007fd9b67f3580> c0387fb6 d97f0000 1a000000 00000000
<00007fd9b67f3590> 1a000000 00000000 6c985f0b da7f0000
<00007fd9b67f35a0> 2c545f0b da7f0000 17351910 00000000
<00007fd9b67f35b0> 30300000 00000000 0ca45f0b da7f0000
<00007fd9b67f35c0> 6cb95f0b da7f0000 0c00f400 7a65fe4c
<00007fd9b67f35d0> 5f000000 00000000 33000000 00000000
<00007fd9b67f35e0> 60367fb6 d97f0000 a4e58b10 00000000 // RBP
<00007fd9b67f35f0> 5f000000 d97f0000 33000000 da7f0000
<00007fd9b67f3600> 80975a14 00000000 0c00f400 7a65fe4c
<00007fd9b67f3610> 0c00f400 7a65fe4c 2c345f0b 10000000
<00007fd9b67f3620> ccb95f0b da7f0000 33000000 00000000
<00007fd9b67f3630> b0377fb6 d97f0000 0ca45f0b da7f0000
<00007fd9b67f3640> 09000000 00000000 08000000 00000000
<00007fd9b67f3650> c4387fb6 d97f0000 00477fb6 d97f0000
<00007fd9b67f3660> d0377fb6 d97f0000 b8478c10 00000000
<00007fd9b67f3670> b83821e9 d97f0000 3b95b600 00000000
<00007fd9b67f3680> c0367fb6 d97f0000 b16bcee3 db7f0000
<00007fd9b67f3690> c0367fb6 d97f0000 e050cee3 db7f0000
<00007fd9b67f36a0> 28000000 00000000 b8a020e9 d97f0000
<00007fd9b67f36b0> 28000000 00000000 2b000000 00000000
",exception,N_USER_OPER,xxxxxxx:32,,,0,,,,,R_SUCCESS,E_NA,,,,,,,,,2024-08-06T16:44:58.560Z,E_NON_TRUE_TO_SOURCE_CSV,"{""build_version"":""37.22.05.0000:8 ng"",""destination"":""master"",""file_name"":""OSinterface.c"",""line"":""2236"",""status"":""UNLOCK_ACTIVE"",""vm_ip"":""xxxxxxx30"",""log_attempts"":""1914"",""logs_sent"":""248""}"
164455.cngss-587f8b9bf4-hckcp.cngss_cngss-587f8b9bf4-hckcp.csv:ERROR,EN_USER,"Task IMS  Segmentation Violation (Signal=11, SIGRTMIN=34) Event=11
Report 5 of 7
Stack dump:
Stack pointer = 00007fd9b67f36c0
Stack dump:
<00007fd9b67f36c0> c0387fb6 d97f0000 1a000000 00000000
<00007fd9b67f36d0> 1a000000 00000000 1a000000 00000000
<00007fd9b67f36e0> 90387fb6 d97f0000 76305611 00000000
<00007fd9b67f36f0> 70357fb6 d97f0000 f0c75212 00000000
<00007fd9b67f3700> 00000000 00000000 00000000 00000000
<00007fd9b67f3710> 01000000 da7f0000 00000000 00000000
<00007fd9b67f3720> 00000000 00000000 00000000 00000000
<00007fd9b67f3730> 2b000000 00000000 14000000 00000000
<00007fd9b67f3740> 10d820e9 d97f0000 a6385611 00000000
<00007fd9b67f3750> 00000000 00000000 2c855509 da7f0000
<00007fd9b67f3760> 2c0d5f0b da7f0000 ffefffff ffff0000
<00007fd9b67f3770> c4387fb6 d97f0000 00477fb6 d97f0000
<00007fd9b67f3780> a0377fb6 d97f0000 0ca45f0b da7f0000
<00007fd9b67f3790> 45000000 00000000 06000000 00000000
<00007fd9b67f37a0> 0851ee14 00000000 0ca45f0b da7f0000
<00007fd9b67f37b0> 30397fb6 d97f0000 ffefffff ffff0000
<00007fd9b67f37c0> c4387fb6 d97f0000 00477fb6 d97f0000
<00007fd9b67f37d0> f0377fb6 d97f0000 98ac5f11 00000000
<00007fd9b67f37e0> ffefffff ffff0000 c0387fb6 d97f0000
<00007fd9b67f37f0> 90387fb6 d97f0000 5ab35f11 00000000
<00007fd9b67f3800> 02000000 db7f0000 15913ee4 db7f0000
<00007fd9b67f3810> 15913ee4 db7f0000 19913ee4 04000000
<00007fd9b67f3820> 00000000 ffffffff 8c339907 da7f0000
<00007fd9b67f3830> ffffffff ffffffff 00000000 00000000
<00007fd9b67f3840> 00000000 00000000 00000000 00000000
<00007fd9b67f3850> 00000000 00000000 00000000 00000000
<00007fd9b67f3860> 00000000 00000000 c0387fb6 d97f0000
<00007fd9b67f3870> 30397fb6 d97f0000 c0387fb6 d97f0000
<00007fd9b67f3880> 30397fb6 d97f0000 ffefffff ffff0000
<00007fd9b67f3890> 803a7fb6 d97f0000 646f6011 00000000
<00007fd9b67f38a0> 00000000 00000000 00000000 00000000
<00007fd9b67f38b0> ffffffff 00000000 00000000 00000000
```
Function Back Trace:
```
Funct Addr   Funct Name                 Line #                          File
===============================================================================
000000001196def7     findMediaPolicyData4NK   1103      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_multiple_sessions_util.cpp
0000000011a6d7ca     MediaPolicy_nk_red_data_rcvCheckpt   1410      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/ims/util/linux_x86-64_csbc_ds_ims/../../../../../../../ssp/ds/ims/util/IMSpolicy_redundancy.cpp
00000000108be59c     STIproc_pard_restore   158      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/ims/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pard_resp.cpp
00000000108c47b0     STIproc_msg           784      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/ims/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_msg.cpp
00000000115fac90     IMSmain               52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/ims/main/linux_x86-64_csbc_ds_ims_proxy/../../../../../../../ssp/ds/ims/main/IMS_main.cpp
```
源代码:
```cpp

 51 void STIproc_pard_restore(IMS_MSG *msg_ptr)
 52 {
 53         APP_PARD_RESTORE_INFO *pard_info = &(msg_ptr->ims_hdr.msg_hdr_data.app_hdr_data.pard_restore_info);
 54
 55         /* Set local data from pard_info */
 56         if ( IS_VALID_PTR(pard_info) == FALSE )
 57         {
 58                 IMS_DLOG( IMS_LOGHIGH, "%s: Invalid pard restore info (%p).", __func__, pard_info);
 59                 return;
 60         }
 61         char * key = pard_info->key;
 62         if ( IS_VALID_PTR(key) == FALSE )
 63         {
 64                 IMS_DLOG( IMS_LOGHIGH, "%s: Invalid restore key (%p).", __func__, key);
 65                 return;
 66         }
 67         uint32_t key_len = pard_info->key_len;
 68         char *data = pard_info->data;
 69         if ( IS_VALID_PTR(data) == FALSE )
 70         {
 71                 IMS_DLOG( IMS_LOGHIGH, "%s: Invalid restore data (%p).", __func__, data);
 72                 return;
 73         }
 74         uint32_t data_len = pard_info->data_len;
 75         NK_SKEY  skey = pard_info->skey;
 76         IMS_RED_DATA_TYPE  data_type = pard_info->data_type;

...
	 MediaPolicy_nk_red_data_rcvCheckpt(key, key_len, data, data_len, data_type, skey)
}

void MediaPolicy_nk_red_data_rcvCheckpt(
        char *key,
        unsigned long key_len,
        char *data,
        unsigned long data_len,
        IMS_RED_DATA_TYPE data_type,
        NK_SKEY skey)

```

```asm
   2 >>>>>>>> Disassembling function: STIproc_pard_restore(ims_MSG*)
   3 0x00000000108be270 <+0>:        55      push   %rbp
   4 0x00000000108be271 <+1>:        48 8d 97 0c f0 ff ff    lea    -0xff4(%rdi),%rdx // 计算APP_PARD_RESTORE_INFO *pard_info， 并放到rdx中（rdi指向传入参数IMS_MSG *msg_ptr）。
   5 0x00000000108be278 <+8>:        48 b8 ff ef ff ff ff ff 00 00   movabs $0xffffffffefff,%rax //把IS_VALID_PTR 的最大合法地址放到 rax
   6 0x00000000108be282 <+18>:       48 8d 4f 0c     lea    0xc(%rdi),%rcx
   7 0x00000000108be286 <+22>:       48 89 e5        mov    %rsp,%rbp //改变rbp 寄存器
   8 0x00000000108be289 <+25>:       41 57   push   %r15 //保存寄存器以防止覆盖丢失数据。
   9 0x00000000108be28b <+27>:       41 56   push   %r14 //保存寄存器以防止覆盖丢失数据。
  10 0x00000000108be28d <+29>:       41 55   push   %r13 //保存寄存器以防止覆盖丢失数据。
  11
  12 0x00000000108be270      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  13 0x00000000108be271      STIproc_pard_restore    56      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  14 0x00000000108be278      STIproc_pard_restore    56      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  15 0x00000000108be282      STIproc_pard_restore    53      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  16 0x00000000108be286      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  17 0x00000000108be289      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  18 0x00000000108be28b      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  19 0x00000000108be28d      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  20 ===================================
  21 0x00000000108be28f <+31>:       41 54   push   %r12 //保存寄存器以防止覆盖丢失数据。
  22 0x00000000108be291 <+33>:       53      push   %rbx //保存寄存器以防止覆盖丢失数据。
  23 0x00000000108be292 <+34>:       48 89 fb        mov    %rdi,%rbx // 把 IMS_MSG *msg_ptr放到rbx 寄存器
  24 0x00000000108be295 <+37>:       48 83 ec 48     sub    $0x48,%rsp // 为局部变量分配栈空间
  25 0x00000000108be299 <+41>:       48 39 c2        cmp    %rax,%rdx // if ( IS_VALID_PTR(pard_info) == FALSE )
  26 0x00000000108be29c <+44>:       0f 87 fe 00 00 00       ja     0x108be3a0 <STIproc_pard_restore(ims_MSG*)+304> // 跳转到0x108be3a0 打印log
  27 0x00000000108be2a2 <+50>:       4c 8b 67 0c     mov    0xc(%rdi),%r12 
  28 0x00000000108be2a6 <+54>:       49 8d 94 24 00 f0 ff ff lea    -0x1000(%r12),%rdx // rdx 存储 char*key
  29
  30 0x00000000108be28f      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  31 0x00000000108be291      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  32 0x00000000108be292      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  33 0x00000000108be295      STIproc_pard_restore    52      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  34 0x00000000108be299      STIproc_pard_restore    56      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  35 0x00000000108be29c      STIproc_pard_restore    56      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  36 0x00000000108be2a2      STIproc_pard_restore    61      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  37 0x00000000108be2a6      STIproc_pard_restore    62      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  38 ===================================
  39 0x00000000108be2ae <+62>:       48 39 c2        cmp    %rax,%rdx
  40 0x00000000108be2b1 <+65>:       0f 87 09 01 00 00       ja     0x108be3c0 <STIproc_pard_restore(ims_MSG*)+336>
  41 0x00000000108be2b7 <+71>:       4c 8b 57 18     mov    0x18(%rdi),%r10
  42 0x00000000108be2bb <+75>:       44 8b 7f 14     mov    0x14(%rdi),%r15d
  43 0x00000000108be2bf <+79>:       49 8d 92 00 f0 ff ff    lea    -0x1000(%r10),%rdx
  44 0x00000000108be2c6 <+86>:       48 39 c2        cmp    %rax,%rdx
  45 0x00000000108be2c9 <+89>:       0f 87 91 00 00 00       ja     0x108be360 <STIproc_pard_restore(ims_MSG*)+240>
  46 0x00000000108be2cf <+95>:       4c 8b 6f 28     mov    0x28(%rdi),%r13
  47
  48 0x00000000108be2ae      STIproc_pard_restore    62      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  49 0x00000000108be2b1      STIproc_pard_restore    62      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  50 0x00000000108be2b7      STIproc_pard_restore    68      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  51 0x00000000108be2bb      STIproc_pard_restore    67      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  52 0x00000000108be2bf      STIproc_pard_restore    69      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  53 0x00000000108be2c6      STIproc_pard_restore    69      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  54 0x00000000108be2c9      STIproc_pard_restore    69      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  55 0x00000000108be2cf      STIproc_pard_restore    75      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  56 ===================================
  57 0x00000000108be2d3 <+99>:       8b 47 20        mov    0x20(%rdi),%eax
  58 0x00000000108be2d6 <+102>:      45 89 fb        mov    %r15d,%r11d
  59 0x00000000108be2d9 <+105>:      44 8b 77 24     mov    0x24(%rdi),%r14d
  60 0x00000000108be2dd <+109>:      4c 89 de        mov    %r11,%rsi
  61 0x00000000108be2e0 <+112>:      4c 89 e7        mov    %r12,%rdi
  62 0x00000000108be2e3 <+115>:      4c 89 55 c0     mov    %r10,-0x40(%rbp)
  63 0x00000000108be2e7 <+119>:      4c 89 5d c8     mov    %r11,-0x38(%rbp)
  64 0x00000000108be2eb <+123>:      89 45 bc        mov    %eax,-0x44(%rbp)
  65
  66 0x00000000108be2d3      STIproc_pard_restore    74      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  67 0x00000000108be2d6      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  68 0x00000000108be2d9      STIproc_pard_restore    76      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  69 0x00000000108be2dd      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  70 0x00000000108be2e0      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  71 0x00000000108be2e3      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  72 0x00000000108be2e7      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  73 0x00000000108be2eb      STIproc_pard_restore    74      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp
  74 ===================================
  75 0x00000000108be2ee <+126>:      e8 bd 51 8d ff  callq  0x101934b0 <key_to_hex>
  76 0x00000000108be2f3 <+131>:      41 b9 40 4c 60 12       mov    $0x12604c40,%r9d
  77 0x00000000108be2f9 <+137>:      48 89 44 24 10  mov    %rax,0x10(%rsp)
  78 0x00000000108be2fe <+142>:      41 b8 e8 18 60 12       mov    $0x126018e8,%r8d
  79 0x00000000108be304 <+148>:      31 c0   xor    %eax,%eax
  80 0x00000000108be306 <+150>:      b9 4d 00 00 00  mov    $0x4d,%ecx
  81 0x00000000108be30b <+155>:      ba c3 09 60 12  mov    $0x126009c3,%edx
  82 0x00000000108be310 <+160>:      be 03 00 00 00  mov    $0x3,%esi
  83
  84 0x00000000108be2ee      STIproc_pard_restore    78      /home/ngl/22.5.0-pp1/R3722.5.20230717_1/obj/linux_x86-64/ssp/ds/im     s/stm_infra/stm_controller/linux_x86-64_csbc_ds_ims/../../../../../../../../ssp/ds/ims/stm_infra/stm_controller/STIproc_pa     rd_resp.cpp


```




<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE0MDA3MDIxNjgsLTE1MzU3MTMzODUsNj
c4MjMxNjUyLDk5ODQyMzAwOCwtMjM2MDI3Nzk5LDU0MTAyMDAw
OCwtNDYwNjkzNzk1LC0xNDA3NzkzMTEyLDY0MDk3NTg1LC0xMj
Y2NjE3MTQ0LC0xNDIxMjU4MDM3LC0zNjAxNjg1NDcsLTEyNDM5
NTYxNDUsMTQ0NzY2NTA4NywxMTYyMTQ2Mjc0LC00OTk2MTY3Mz
IsLTEzMjAyMDE3OTcsMTI3ODAyMjY1LC05OTcwMjE4NDksLTIx
MTEwMTgyMTJdfQ==
-->