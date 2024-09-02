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

ELF header: 在ELF 最开始，包含了描述整个文件的组织的信息。
Section: 包含了从Linking 角度看到的object file 里的所有信息: 
##

<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE1NDQzMjc3MCwtMTkyOTYxMTM5LC0xMT
I0OTYyNDczLDIwMDY5NDY1MjIsNzMwOTk4MTE2XX0=
-->