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

**32-bit数据类型**

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/elf32_datatype.png)

**64-bit 数据类型**

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/elf64_datatype.png)

所有由object文件格式定义的数据结构都遵循相关类别的自然大小和**对齐**指南。如果有必要，数据结构会包含显式填充，以确保4字节对象的4字节对齐，强制结构的大小为4的倍数，等等。数据从文件的开头起也具有适当的对齐。因此，例如，一个包含 `Elf32_Addr` 成员的结构将在文件中**以4字节边界对齐**，而包含 `Elf64_Addr` 成员的结构将在**8字节边界对齐**。

## ELF Header

某些object文件**控制结构**可能会增长，因为 ELF 头包含它们的实际大小。如果目标文件格式发生变化，程序可能会遇到比预期更大或更小的控制结构。因此，程序可能会忽略多余的信息。对于缺失信息的处理取决于上下文，并将在定义扩展时予以指定。

ELF header 结构体定义在文件  sys/elf.h:

```c
#define EI_NIDENT       16
 
typedef struct {
        unsigned char   e_ident[EI_NIDENT]; 
        Elf32_Half      e_type;
        Elf32_Half      e_machine;
        Elf32_Word      e_version;
        Elf32_Addr      e_entry;
        Elf32_Off       e_phoff;
        Elf32_Off       e_shoff;
        Elf32_Word      e_flags;
        Elf32_Half      e_ehsize;
        Elf32_Half      e_phentsize;
        Elf32_Half      e_phnum;
        Elf32_Half      e_shentsize;
        Elf32_Half      e_shnum;
        Elf32_Half      e_shstrndx;
} Elf32_Ehdr;

typedef struct {
        unsigned char   e_ident[EI_NIDENT]; 
        Elf64_Half      e_type;
        Elf64_Half      e_machine;
        Elf64_Word      e_version;
        Elf64_Addr      e_entry;
        Elf64_Off       e_phoff;
        Elf64_Off       e_shoff;
        Elf64_Word      e_flags;
        Elf64_Half      e_ehsize;
        Elf64_Half      e_phentsize;
        Elf64_Half      e_phnum;
        Elf64_Half      e_shentsize;
        Elf64_Half      e_shnum;
        Elf64_Half      e_shstrndx;
} Elf64_Ehdr;
```
**一个例子：**
```
$ readelf -h cngss.elf
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 03 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - GNU
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Advanced Micro Devices X86-64
  Version:                           0x1
  Entry point address:               0x100b4000
  Start of program headers:          64 (bytes into file)
  Start of section headers:          532462488 (bytes into file)
  Flags:                             0x0
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         8
  Size of section headers:           64 (bytes)
  Number of section headers:         48
  Section header string table index: 47

```

- **e_ident**(16 bytes):  elf identification。 ELF刚开始的16 byte 用来标识这个文件是一个object file。 提供其他的机器无关的数据，用来decode和解析这个文件的内容。
	- **前四个byte** 是Magic Number: 7f 45 4c 46 (0x7f, 'E', 'L', 'F'): e_ident[0] ~ e_ident[3]
	- **第五个**byte是Class: 32-bit(1) or 64 bit(2), 0 表示非法。32-bit 最大虚拟地址空间是4G
	- **第六个**byte是Data  编码方式: little endian(1) 和big endian(2)， 0表示非法。
	`00000001： 2's complement, little endian`
	- **第七个**byte是ELF header Version
	- **第八个**byte是这个object file 的目的 操作系统和ABI。
	- 第九个byte： -   ELF（可执行和可链接格式）文件中的 `e_ident[EI_ABIVERSION]` 字节用于标识该对象文件目标的 ABI 版本。这个字段用于区分 ABI 的不兼容版本。该版本号的解释取决于由 `EI_OSABI` 字段标识的 ABI。如果处理器没有为 `EI_OSABI` 字段指定特定值，或者没有为 `EI_OSABI` 字节的某个特定值确定的 ABI 指定版本值，则使用 0 表示未指定。
	- 其他的是留作将来用的。
- **e_type** (2 bytes): 这个object file 的类型。
	- 0: No file type
	- 1: Relocatable file
	- 2: Executable file
	- 3: Shared object file
	- 4: Core file
	- 0xff00: Processor-specific
	- 0xffff: Processor-specific
- **e_machine**  (2 bytes) : 运行所需要的architecture
	- 0: No Machine
	- 2: SPARC
	- 3: Intel 80386
	- 18: SUn SPARC 32+
	- 43: SPARC V9
	- 其他的新的定义在sys/elf.h
- **e_version**(4 bytes): object file version.
- **e_entry**(4 or 8 bytes):  程序的入口地址。
- **e_phoff**(4 or 8 bytes): Program Header table的从文件开头开始的偏移量(bytes), 如果没有Program Header table, 值是0。
- **e_shoff** (4 or 8 bytes): Section Header table的从文件开头开始的偏移量。如果没有Section Header table, 值是0。
- **e_flags** (4 bytes):  processor-specific的flag。 flag 名字可以从EF_machine_flag找到。当前对于IA， 这个flag 是0。
- **e_ehsize**(2 bytes): ELF Header 的size。
- **e_phentsize**(2 bytes): Program Header table里的一个entry 的以byte 为单位的大小。每一个entry的大小都是一样的。
- **e_phnum**(2 bytes): Program header table里的 entry的个数。
- **e_shentsize**(2 bytes): Section Header table里的一个entry 的以byte 为单位的大小。每一个entry的大小都是一样的。
- **e_shnum**(2 bytes): Section header table里的 entry的个数。
- **e_shstrndx**(2 bytes):  指向Section Header table里的一个特殊的entry的index， 这个section 包含了section name string table。

## Section 
object file 里的Section Header Table 保存着定位所有section 所需要的信息。Secion Header Table 起始偏移量是e_shoff 定义， 每一个section的大小是由e_shentsize 定义， section 的数量是由e_shnum定义。

一些特殊的Section Header Table的index 是被预留了， 不能被object file 使用。
![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/special_section_index.png)

- SHN_UNDEF: index 0 标识 没有定义的，丢失的， 不相关的，或者没有意义的section。
	> 尽管index0 是预留的， 但是section header table里包含一个index 是0 的entry。如果ELF header 里面的e_shnum是6， 那么他们的index 是 从0 到5。
- SHN_LORESERVE: 预留的index范围的最小值。
- SHN_LOPROC 到 SHN_HIPROC, 这个范围内的index 是processor-specific的。
- SHN_ABS: 该值为相应的引用指定了绝对值。例如，相对于section number SHN_ABS 定义的符号具有绝对值，不受重定位的影响。
- SHN_COMMON: 与此section相关的符号是common符号例。如 FORTRAN 的 COMMON 或未分配的 C 语言外部变量
- SHN_HIRESERVE: 预留的index的最大值。预留的index是从SHN_LORESERVE到SHN_HIRESERVE, 包含这两值。这些值不引用Section Header table。也就是说，Section Header table中不包含这些保留索引的条目


Section 包含了object file 里的除了ELF Header， Program Header Table， Section Header Table 的所有的信息。section 满足以下一些条件：

 - 每一个section 有一个section header用来描述它， section header 可以没有section 而单独存在。
 - 每一个section 占有连续的字节序列， 可能是空的。
 - section直接不能重叠。没有一个byte 可以在两个section里。
 - object 中可能存在未使用的空间。各种header 和section可能并未覆盖目标文件中的每个字节。未使用数据的内容未作规定。

### Section Header 定义在 sys/elf.h里
```
typedef struct {
        Elf32_Word      sh_name;
        Elf32_Word      sh_type;
        Elf32_Word      sh_flags;
        Elf32_Addr      sh_addr;
        Elf32_Off       sh_offset;
        Elf32_Word      sh_size;
        Elf32_Word      sh_link;
        Elf32_Word      sh_info;
        Elf32_Word      sh_addralign;
        Elf32_Word      sh_entsize;
} Elf32_Shdr;

typedef struct {
        Elf64_Word      sh_name;
        Elf64_Word      sh_type;
        Elf64_Xword     sh_flags;
        Elf64_Addr      sh_addr;
        Elf64_Off       sh_offset;
        Elf64_Xword     sh_size;
        Elf64_Word      sh_link;
        Elf64_Word      sh_info;
        Elf64_Xword     sh_addralign;
        Elf64_Xword     sh_entsize;
} Elf64_Shdr;
```
- **sh_name**: section的名字。它的值section header string table的index， 是一个有字符串结束的字符串。
- sh_type: section 的类型。
- sh_flags: 描述section的一些bit 属性。
- sh_addr: 如果这个secion 要出现在进程内存镜像中， 这个值指定了起始地址。否则这个值是0.
- sh_offset: 这个secion 从object 开始算气的pianyiliang

### 特殊section

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/special_section_name.png)

## 


<!--stackedit_data:
eyJoaXN0b3J5IjpbNjM2MDM4NDk4LDgxMjk3Nzc3LDEyNzU0Nz
E1NzUsLTYwNDYyODQzNiwtMTEzMDYyMzU5OCwxOTgyNDI0NzAw
LC0xODY1NzI1Mzc0LC0xOTIzNzgwMzY0LDg1MTAyNDc1MCwxOT
cxNTY2NzQ5LDYyMTcwMzk4NSw2NDk2NzYzNjUsMTU0Mzg2Nzg1
MiwtNTk1NTY0NDc0LDEyOTk5MzAyNjYsNTYwMjI0NTQ2LC0xNj
MyNTEzNjk2LDEyMTYzOTg5LDM3OTgwODk3NywxNzY2ODIwNjAx
XX0=
-->