
# 寄存器

1. 通用寄存器。
	1. **RDX/EDX**。EDX 是32 bit，RDX是64 bit。 在64位系统里， EDX是RDX的低32位。
	    在函数调用中， RDX 在调用函数前，用来存储被调用函数的**第三个参数**。
	3. 
2. xxx

# 汇编指令
1. **mov**: 从源从组数拷贝数据到目的操作数.
	```asm
	mov $0x12501f00,%edx
	// $ 开头表示这是一个立即数。
	
	```
2. xor: 位异或。 当两位相等时，结果是0， 否则是1。
	```asm
	xor %eax, %eax //清空eax, 效率比复制要高。 这个操作会使rax 的高32bit 一会被清零。
	```
4. 
<!--stackedit_data:
eyJoaXN0b3J5IjpbOTg2NjA5Mzk1LC05MzYxMzE3NTYsLTI3MD
QzMTU5MCwtMTU4MTQ5ODc5MSw3MzA5OTgxMTZdfQ==
-->