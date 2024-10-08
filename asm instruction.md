
# 寄存器

1. **通用寄存器**
	1. **RDX/EDX**。EDX 是32 bit，RDX是64 bit。 在64位系统里， EDX是RDX的低32位。
	    - 在函数调用中， RDX 在调用函数前，用来存储被调用函数的**第三个参数**。
	2. **RAX/EAX**。 EAX是32 bit， RAX是64 bit。 在64 位系统中， EAX是RAX的低32位。
		- RAX/EAX 用来保存函数调用后的返回值。
	4. **RCX/ECX**。ECX是32 bit， RCX是64 bit。 在64 位系统中， ECX是RCX的低32位。
		- 在**函数调用**中， RDX 在调用函数前，用来存储被调用函数的**第四个参数**。
		- 在**LOOP， LOOPZ**等指令中， 作为loop 计数器。这些指令会使寄存器中的计数器每次减一， 直到减到零。
		- 在**位操作指令**SHL, SHR中， 用来指定移动的位数。

	5. **RBX/EBX**。EBX是32 bit， RBX是64 bit。 在64 位系统中， EBX是RBX的低32位。
		- 用作base pointer
		- 保存跨函数调用不变的数据
		- 在函数调用时， RBX是一个被调用函数保存的寄存器。意思是如果一个函数要是用RBX寄存器， 它必须要把原来的值存到栈上， 然后在退出前恢复。
	6. **R12, R13, R14, R15**。
		- 和RBX一样用来保存跨函数调用不变的数据。
		- 和RBX一样， 在函数调用时， 这些寄存器是需要被调用函数保存的寄存器。意思是如果一个函数要是用这些寄存器， 它必须要把原来的值存到栈上， 然后在退出前恢复。
	7. 
2. **RIP(EIP) 指令寄存器**。保存下一条CPU要执行的指令的内存地址。
	RIP的内容会被自动指向下一条或者跳转(call, jump, return)。
4. RSP(stack pointer register)
5. RBP(frame pointer register)

# 汇编指令
 1. **mov**: 从源从组数拷贝数据到目的操作数。如果目的操作数是栈， RSP会被自动修改。
	```asm
	mov $0x12501f00,%edx
	// $ 开头表示这是一个立即数。	
	```
	**movl**: 拷贝32bit data
	
	**movq**: 拷贝64bit data
	
	**movabs**: 把大的立即数(large than 32-bit)直接拷贝到寄存器中。

	**movzwl**： 把16位的源数据拷贝到32位的目的， 扩展部分补0.
	```asm
	movzwl (%rbx),%eax
	```
	
 2. **xor**: 位异或。 当两位相等时，结果是0， 否则是1。
	```asm
	xor %eax, %eax //清空eax, 效率比复制要高。 这个操作会使rax 的高32bit 也被清零。
	```
	
 3.  **pushq（push in 32 bit）**: 把一个值save到栈上。栈是一个先进后出的内存。RBP(EBP)指向栈底， RSP(ESP)指向栈顶。栈是从高地址向低地址长。 例如：  **push eax**  ; RSP减8(在32-bit，ESP 减4) 同时把 EAX 地址存在栈上。
 
 4.  **popq(pop in 32 bit)**: 从栈顶删掉一个值， 并把这个值放到寄存器里或者某个地址上。 例如：**pop ebx**  ; 把指定值拷贝到EBX 寄存器, RSP加8（在32-bit, ESP 加4)。
 
5.  **callq(call in 32 bit)**: 用来调用一个函数。它会保存下一条指令(返回指令)到栈上， 然后跳到函数的地址去执行（把函数地址放到RIP/EIP 寄存器中）。 例如:  **call my_function**  ; 把下一条指令地址放到栈上(返回地址)。 跳到 'my_function' 的地址去执行。

6.  **retq(32 bit 是ret)**: 从函数调用中返回， 保存在栈上的返回地址(调用函数中在调用被调函数的时候call 指令的时候入栈的）被放到RIP/EIP 中去继续执行。此时返回地址是在栈顶的。64-bit 上是**retq**。地址在64-bit上是8 byte， 在32-bit上是4 byte。 例如：ret ; 从栈顶取出返回地址， 从这个地址继续执行。

7. **leaveq(leave in 32 bit)**: 用在retq/ret前， 在函数返回前做一些cleanup的工作:
	- **movq %rbp, %rsp**: 把上一个函数的rbp 放到rsp中，这样就把这个函数得到局部变量和其他的data 从栈上清除了。
	- **popq %rbp**: 把栈上保存的上一个函数的rbp 放到rbp 寄存器中。这样栈就退到了上一个函数的栈。

8. **lea(Load Effective Address)**:  和mov不一样的是， 这个指令不会访问地址所指向的内存， 只是会计算并把地址赋给目的操作数。
	```asm
	lea -0xff4(%rdi), %rdx //计算地址 %rdi - 0xff4, 然后把地址放到rdx中。
	``` 
	
9. **cmp**: 比较两个操作数（从第二个操作数中减去第一个操作数）， 它不会存储结果，但是会设置flag （Zero Flag, Sign flag, Carry Flag, Overflow Flag）, 这些flag 会被接下来的条件指令使用(jz, jnz, jg, jl)
	```asm
	cmp %rax, %rdx
	ja 0x108be3a0 <STIproc_pard_restore(ims_MSG*)+304> 
	```
	
	**cmpb**: 只比较一个字节:
	```asm	
	cmpb   $0x5,0x17909dc(%rip)
	```
	
	**cmpw**: 比较2个bytes
	
10.  **jmp**: 跳转到地址。会修改指令寄存器RIP(EIP)

	  **ja**: jmp if above.
	  检查两个Flag:
	  1) Zero Flag must be clear(not set), meaning the result is NOT zero.
	  2) Carry Flag must be clear, no carry from the most significant bit)
	  等价于第一个操作数大于第二个操作数。
	  
	  **je**: jmp if equal (check Zero Flag if equal to 0)
	  
	  **jg**: jump if greater（True: Zero Flag is 0, Sign Flag (SF)is equal to the Overflow Flag (OF)）
	  
	  **jl**: jump if less
	  
	  **jz**: jump if zero
	  
	  **jnz**: jump if NOT zero
	  
12.  **add**： 把第一个操作数加到第二个操作数上。 add 指令会set相应的flag。

```asm
	add $0x1c8,%rsp
```
	
12. **test**: 做按位 AND操作， 和add 不同的是， 结果不会保存，只是会设置一些flag。
if the result is 0, then **Zero Flag** will be set.

If the most significant bit(indicate the result is negative)  is 1, the **Sign Flag** will be set.

Other flags

	```asm
	test   %r12, %r12 // 判断r12是不是0. 如果等于0， set Zero Flag。
	jz     some_label
	```
14. **sub**: 把第一个操作数从第二个操作数中减去。 sub 指令会set相应的flag.
	```asm
	sub 0x30 $(rsp)
	``` 
15. **nop** : 这种指令不会进行任何的操作， 只是用来进行指令对齐， 以便让cpu 更高效的对指令进行存取。 下面不同的指令指令长度不一样。
	```asm
	nop
	```

	  **nopw**
	  ```asm
	   nopw   0x0(%rax,%rax,1)
	```
	0x0(%rax, $rbx, 2): 这是一种计算地址的方式： 地址= 0x0 + (%rax + %rbx * 2)
	  
	  **nopl**
	  ```asm
	nopl 0x0(%eax,%eax,1)
	  ```
16. xxx
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE2MDEwODM4OTYsLTQ2NTIxMzY5Miw0Mj
A0OTIxMjcsLTg2MjkyOTA1Miw5ODM4NjM1NDEsMTk3NzE3OTk4
Nyw1OTE0Mzk0ODUsLTEwMTUyNjcxODcsLTExNDUzNDAzMzksLT
E4NTU0OTQxNDksNDQ1MjQwNDI4LC0xMTIzNDQ1NjIxLC0xMTgx
MDk1NTEsLTIwNDg3NDQ5OTcsMTk5NDgwNjg0Myw1MzA2NTYxOC
wxNzYzNDQ0OTE1LDk1MzUxODM2Nyw5ODY2MDkzOTUsLTkzNjEz
MTc1Nl19
-->