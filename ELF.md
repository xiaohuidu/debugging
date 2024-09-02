# ELF 和 ABI
- ELF: Executable and Linking Format.
- ABI: Application Binary Interface
ELF 格式是在  **System V Application Binary Interface** 中定义的。ELF最开始是作为ABI的一部分在UNIX System Laboratories(USL) 而进行开发和发布的。 后来 Tool Interface Standards 委员会(TIS) 选择把ELF 作为object 文件的标准格式。 
# ELF 
有三种object 文件:
- 可重定位object 文件(relocatable file): 适合和其他的object文件一起创建可执行文件或者共享文件。
- 可执行文件(Executable file)
- 共享文件(Shared object file): 和其他可重定位文件或者共享文件来创建另一个object 文件；和可执行文件和其他的
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA1OTA1ODc0NiwtMTkyOTYxMTM5LC0xMT
I0OTYyNDczLDIwMDY5NDY1MjIsNzMwOTk4MTE2XX0=
-->