
# 寄存器

1. **通用寄存器**
	1. **RDX/EDX**。EDX 是32 bit，RDX是64 bit。 在64位系统里， EDX是RDX的低32位。
	    - 在函数调用中， RDX 在调用函数前，用来存储被调用函数的**第三个参数**。
	2. **RAX/EAX**。 EAX是32 bit， RAX是64 bit。 在64 位系统中， EAX是RAX的低32位。
	3. **RCX/ECX**。ECX是32 bit， RCX是64 bit。 在64 位系统中， ECX是RCX的低32位。
		- 在**函数调用**中， RDX 在调用函数前，用来存储被调用函数的**第四个参数**。
		- 在**LOOP， LOOPZ**等指令中， 作为loop 计数器。这些指令会使寄存器中的计数器每次减一， 直到减到零。
		- 在**位操作指令**SHL, SHR中， 用来指定移动的位数。

	4. **RBX/EBX**。EBX是32 bit， RBX是64 bit。 在64 位系统中， EBX是RBX的低32位。
		- 用作base pointer
		- 保存跨函数调用不变的数据
		- 在函数调用时， RBX是一个被调用函数保存的寄存器。意思是如果一个函数要是用RBX寄存器， 它必须要把原来的值存到栈上， 然后在退出前恢复。
	6. xx
2. xxx

# 汇编指令
1. **mov**: 从源从组数拷贝数据到目的操作数。如果目的操作数是栈， RSP会被自动修改。
	```asm
	mov $0x12501f00,%edx
	// $ 开头表示这是一个立即数。
	
	```
2. xor: 位异或。 当两位相等时，结果是0， 否则是1。
	```asm
	xor %eax, %eax //清空eax, 效率比复制要高。 这个操作会使rax 的高32bit 也被清零。
	```
4. 
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjAxNDAyMzM0MCw5NTM1MTgzNjcsOTg2Nj
A5Mzk1LC05MzYxMzE3NTYsLTI3MDQzMTU5MCwtMTU4MTQ5ODc5
MSw3MzA5OTgxMTZdfQ==
-->