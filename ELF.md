# ELF 和 ABI
- ELF: Executable and Linking Format.
- ABI: Application Binary Interface
ELF 格式是在  **System V Application Binary Interface** 中定义的。ELF最开始是作为ABI的一部分在UNIX System Laboratories(USL) 而进行开发和发布的。 后来 Tool Interface Standards 委员会(TIS) 选择把ELF 作为object 文件的标准格式。 
# ELF 
有三种object 文件:
- **可重定位object 文件(relocatable file)**: 适合和其他的object文件一起创建可执行文件或者共享文件。
- **可执行文件(Executable file)**
- **共享文件(Shared object file)**: 和其他可重定位文件或者共享文件来创建另一个object 文件；和可执行文件和其他的共享文件一起创建进程镜像。

## ELF 文件格式
Object 文件参与一个程序的 build 和execution， 所以object 文件的格式可以从两个不同的角度来看。

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/ELF_format.png)

**ELF header**: 在ELF 最开始，包含了描述整个文件的组织的信息。

**Program header table**: 这个部分是可以没有的。如果存在， 是用来告诉系统如何去创建进程的image（可执行的程序）。可执行文件必须要有Program header table，可重定位object 不需要有Program header table。

**Section header table**: 包含了描述section的信息。每一个section 在Section header table 里有一条记录。每一条记录包含了section的基本信息， 比如section 名字， section大小...

**Section**: 包含了从Linking 角度看到的object file 里的所有信息: instructions, data, symbol table, relocation information...

> **Program header table** 和**Section header table** 的位置不是固定的，只有**ELF header** 的位置是固定的在文件最开始处。
## 数据类型

ELF32:
![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/elf32_datatype.png)

##
##

<!--stackedit_data:
eyJoaXN0b3J5IjpbNDE5NDU3NDk5LDEzNjA5MDkyMTAsLTE3Nj
U1MTY0NCwtMTkyOTYxMTM5LC0xMTI0OTYyNDczLDIwMDY5NDY1
MjIsNzMwOTk4MTE2XX0=
-->