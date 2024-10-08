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
object file 里的**Section Header Table** 保存着定位所有section 所需要的信息。Secion Header Table 起始偏移量是e_shoff 定义， 每一个section的大小是由e_shentsize 定义， section 的数量是由e_shnum定义。

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
- **sh_type**: section 的类型。
- **sh_flags**: 描述section的一些bit 属性。
- **sh_addr**: 如果这个secion 要出现在进程内存镜像中， 这个值指定了起始地址。否则这个值是0.
- **sh_offset**: 这个secion 从object 开始算起的单位是byte的偏移量。SHT_NOBITS类型的section在object file 中不占用空间， 它的offset 只是概念上定位的。
- **sh_size**: section的大小， 单位是byte。
- **sh_link**: section header table index的link， 值的解释和section 类型相关。
- **sh_info**. 额外的信息。值的解释和section 类型相关。
	> 对于符号表(Symbol Table) Section，这个值表示第一个非Local 符号的符号表index。
- **sh_addralign**: 有些section有地址对齐的限制。例如，如果一个section包含双字（double-word），系统必须确保整个section的双字对齐。也就是说，`sh_addr` 的值除以 `sh_addralign` 的值， 余数必须是0。目前，只允许 0 和正整数次方的 2。值 0 和 1 表示section没有对齐限制。
- **sh_entsize**: 某些section包含固定大小条目的表，例如符号表。对于这样的section，该member给出了每个条目的字节大小。如果该section不包含固定大小的条目表，则该成员的值为 0。 

### Section 类型(sh_type)
|Name| Value |
|--|--|
| SHT_NULL | 0 |
| SHT_PROGBITS | 1 |
| SHT_SYMTAB | 2 |
| SHT_STRTAB | 3 |
| SHT_RELA | 4 |
| SHT_HASH | 5 |
| SHT_DYNAMIC | 6 |
| SHT_NOTE | 7 |
| SHT_NOBITS | 8 |
| SHT_REL | 9 |
| SHT_SHLIB | 10 |
| SHT_DYNSYM | 11 |
| SHT_SUNW_move | 0x6ffffffa |
| SHT_SUNW_move | 0x6ffffffb|
| SHT_SUNW_syminfo | 0x6ffffffc|
| SHT_SUNW_verdef| 0x6ffffffd |
| SHT_SUNW_verneed| 0x6ffffffe|
| SHT_SUNW_versym| 0x6fffffff|
| SHT_LOPROC| 0x70000000|
| SHT_HIPROC| 0x7fffffff|
| SHT_LOUSER| 0x80000000|
| SHT_HIUSER | 0xffffffff |


- **SHT_NULL**: 这个section header inactive，没有关联的section，这个section header的其他的field 没有意义。
- **SHT_PROGBITS**: 程序定义的信息， format 和意义有应用自己定义和解释。
- **SHT_SYMTAB** and **SHT_DYNSYM**: 标识 符号表(symbol table)。 一般的SHT_SYMTAB section 提供link-editing 的符号， 一个完整的符号表一般包含了一些不是动态linking 所必须的一些信息， SHT_DYNSYM section 提供了最小集合的动态linking 所需的符号。
- **SHT_STRTAB**, SHT_DYNSTR**strong text**: 字符串表section。一个object file 可以有多个字符串表section。
- **SHT_RELA**: 标识有显式家数的relocation entries。比如 类型Elf32_Rela 适用于32位的 object file。 一个object file 可以有多个SHT_RELA section。
- **SHT_HASH**:  标识一个symbol哈希表。所有动态链接的object file 必须要有一个 符号哈希表。现在每一个object file 只能有一个符号哈希表， 将来有可能会取消这个限制。
	> 支持符号表访问的哈希表由 Elf32_Word 或 Elf64_Word 对象组成。与哈希相关联的symbole table在哈希表section header的 sh_link 条目中指定。下面出现的label用于帮助解释哈希表的结构，但它们不属于规范的一部分。
	
![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/symbol_hash_table.png)

bucket组包含 `nbucket` 个条目，链数组包含 `nchain` 个条目，索引从 0 开始。bucket和链都保存符号表的index。链表的条目与符号表平行。符号表的条目数应等于 `nchain`，因此符号表index也用于选择链表条目。

哈希函数接受一个符号名称并返回一个值，该值可用于计算bucket index。因此，如果哈希函数对某个名称返回值 `x`，则 `bucket[x%nbucket]` 会给出符号表和链表中的index `y`。如果符号表中的条目不是所需的那个，`chain[y]` 会给出下一个具有相同哈希值的符号表条目。

你可以沿着链表链接前进，直到选定的符号表条目包含所需的名称，或者链表条目包含值 `STN_UNDEF`。

哈希函数如下:
```c
unsigned long
elf_Hash(const unsigned char *name)
{
    unsigned long h = 0, g;
 
	    while (*name)
	    {
		     h = (h << 4) + *name++;
		     if (g = h & 0xf0000000)
			      h ^= g >> 24;
				   h &= ~g;
	    }
	    return h;
}
```


- **SHT_DYNAMIC**: 标识动态链接需要的信息。 每一个object file 只能有一个dynamic section。

- **SHT_NOTE**:
- **SHT_NOBITS**:
- **SHT_REL**:
- **SHT_SHLIB**: 标识没有定义的预留section。包含这种section的object file 不符合ABI。
- **SHT_SUNW_COMDAT**: 这个section 允许相同的data 的多个copy 减少的一个copy。
	> Comdata section 由他们的名字(sh_name) 唯一的标识。如果link-editor遇到多个拥有相同section 名字的SHT_SUNW_COMDAT section， 第一个会被保留，其他的会被丢弃。任何的作用到被丢弃的 SHT_SUNW_COMDAT section的relocation会被忽略， 任何的定义在被丢弃的section里的Symbol 不会被保留。
	
	> 此外，当编译器使用 `-xF` 选项调用时，link-editor也支持用于section重新排序的节命名约定。如果这些section被放置在名称为 `.funcname%sectname` 的节中，那么最终保留的 `SHT_SUNW_COMDAT` 节将合并到由 `.sectname` 标识的section中。通过这种方法，`SHT_SUNW_COMDAT` section 可以被放置在 `.text`、`.data` 或其他任何作为其最终目的地的section中。
- **SHT_SUNW_move**: 标识处理部分初始化的符号的data。
	> 典型的， 在ELF 文件里， 初始化的data maintain 在object 文件内部。如果一个变量非常大，并且只是包含了少量的初始化的（非零）元素， 整个变量还是会maintain在object 文件内部。
	
	> 如果object 包含了大量的部分初始化的变量， 比如FORTRAN_COMMON blocks, 能够引起冥想的disk overhead。SHT_SUNW_move section 提供了数据压缩的一种机制，能够压缩数据减少disk的大小。

	> SHT_SUNW_move section 包含多个类型是ELF32_Move或者ELF64_Move的entry。这些entry允许变量被定义为不确定项(.bss), 所以在object文件中不占大小， 但是在运行时会contribute到process的object memory。move 记录如何在运行时初始化process的内存镜像。
	
```c
typedef struct {
        Elf32_Lword       m_value;
        Elf32_Word        m_info;
        Elf32_Word        m_poffset;
        Elf32_Half        m_repeat;
        Elf32_Half        m_stride;
} Elf32_Move;

#define ELF32_M_SYM(info)       ((info)>>8)
#define ELF32_M_SIZE(info)      ((unsigned char)(info))
#define ELF32_M_INFO(sym, size) (((sym)<<8)+(unsigned char)(size))

typedef struct {
        Elf64_Lword       m_value;
        Elf64_Xword       m_info;
        Elf64_Xword       m_poffset;
        Elf64_Half        m_repeat;
        Elf64_Half        m_stride;
} Elf64_Move;

#define ELF64_M_SYM(info)       ((info)>>8)
#define ELF64_M_SIZE(info)      ((unsigned char)(info))
#define ELF64_M_INFO(sym, size) (((sym)<<8)+(unsigned char)(size))

1. m_value: 初始化值， 将会被move到内存镜像中的值。
2. m_info: 符号表index，与初始化所应用的对象有关，同时也包括被初始化的偏移量的大小（以字节为单位）。m_info的低8位定义了大小，可以是1、2、4或8字节。高字节定义符号index。
3. m_poffset:相对于初始化所应用的关联符号的偏移量。
4. m_repeat: 重复次数。
5. m_stride: 步长计数。该值表示在执行重复初始化时应跳过的单位数量。一个单位的大小由 m_info 定义的初始化对象决定。m_stride 的值为 0 表示初始化将连续执行 m_repeat 个单位。

下面数据定义如果没有压缩占用0x8000 bytes的大小。
typedef struct {
        int     one;
        char    two;
} Data

Data move[0x1000] = {
        {0, 0},       {1, '1'},     {0, 0},
        {0xf, 'F'},   {0xf, 'F'},   {0, 0},
        {0xe, 'E'},   {0, 0},       {0xe, 'E'}
};
使用SHT_SUNW_move section， 数据会被移到 .bss section中， 使用相对应的 move entries进行初始化。
$ **elfdump -s data | fgrep move**
      [17]  0x00020868 0x00008000  OBJT GLOB 0   .bss       move
$ **elfdump -m data**

Move Section: .SUNW_move
        offset  ndx     size    repeat  stride  value   with respect to
        0x8     0x17    4       1       0       0x1     move
        0xc     0x17    1       1       0       0x31    move
        0x18    0x17    4       2       2       0xf     move
        0x1c    0x17    1       2       8       0x46    move
        0x28    0x17    4       2       4       0xe     move
        0x2c    0x17    1       2       16      0x45    move
从relocatable目标文件提供的section会被连接编辑器（link-editor）拼接并输出到正在创建的object文件中。然而，以下条件会导致连接编辑器处理move entries 并将其内容扩展为传统数据项：
1.  输出文件是静态可执行文件。
2.  移动条目的大小大于其数据将要扩展的符号的大小。
3.  使用了 `-z nopartial` 选项。
```

xxx

- xx

### 特殊section

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/special_section_name.png)

#### 字符串表(String Table)
String table sections 里面存储了有结束符的字符串。 object file里的symbol 和section name 存了指向这个table 的index。

string table的第一个byte 是 '\0',  他的index 是0， 最后一个byte 也是'\0'， 这样所有的字符都是有结束符的。如果一个string index 是0 ， 那么它可以使没有name 或者 是一个空name。

一个空的字符串表section 是允许的。section header里的 sh_size 是0， 非零的index 对于一个空的字符串表是不合法的。

section header 的sh_name 存了一个 字符串表的index， 这个section table section 是由 e_shstrndx 指定的。

下面这个字符串表包含了25个bytes 和对应的字符串的index。

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/string_table_example.gif)

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/string_table_example_strings.png)

index 可以指向任意byte， 指向也是被允许的。


#### 符号表(Symbol Table)
object 文件的符号表包含了对符号定义已经引用进行定位和重定位所需要的所有的信息。
symbol table index 是对这个数组的一个索引。 index 0 是第一个也是没有define的一个index。
符号表的entry定义在 sys/elf.h中
```
typedef struct {
        Elf32_Word      st_name;
        Elf32_Addr      st_value;
        Elf32_Word      st_size;
        unsigned char   st_info;
        unsigned char   st_other;
        Elf32_Half      st_shndx;
} Elf32_Sym;

typedef struct {
        Elf64_Word      st_name;
        unsigned char   st_info;
        unsigned char   st_other;
        Elf64_Half      st_shndx;
        Elf64_Addr      st_value;
        Elf64_Xword     st_size;
} Elf64_Sym;
```

- **st_name**: 指向字符串表的index。 symbol的名字。
- **st_value**:  symbol 对应的内容， 他可以是绝对值， 地址和其他的。不同的类型对值的解析是不一样的。
	> 在**relocatable file**中， 对于节index为SHN_COMMON的符号，`st_value`字段表示该符号的对齐约束。
	在**relocatable file**中，`st_value`字段表示已定义符号的secion偏移量。也就是说，`st_value`是相对于`st_shndx`标识的section开头的偏移量。
	在**executable 和shared object file**里， st_value 表示一个虚拟地址。为了使文件中的符号在运行时链接器中更有用， section offset（file interpretation）让位于 虚拟地址( memory interpretation)， 此时section number 已不再重要。
	> 尽管符号表的value 对于不同的object file 有类似的意思， 但这些数据允许不同的程序高效的访问。
- **st_size**: 很多符号是有size的， 比如一个数据对象的size 是这个对象所包含的字节数。如果符号没有size或者不知道size， st_size会是0.
- **st_info**: 符号的类型和绑定属性。下面表列出了一些值及其意义。
绑定属性:
![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/symbol_binding.png)

	> `STB_LOCA`: Local Symbol。只在所在的object file 里可见。不同的object file 允许出现相同的名字。
	`STB_GLOCAL`: Global symbols。合并在一起的所有的object file 都可见。
	`STB_WEAK`: Weak Symbols。类似global symbols， 但是他们的定义有更低的优先级。
	`STB_LOOS`-`STB_HIOS`: OS-specific的预留类型。
	`STB_LPROC`-`STB_HIPROC`: processor-specific的预留类型。

	>`Global 和Weak 符号的区别`:
		- 当编译器把多个relocatable object file合并在一起的时候， 不允许出现多个相同的global的符号， 但是允许出现相同名字的weak的符号。global的符号会覆盖weak的符号。(common 符号也会覆盖weak符号)
		- 当链接编辑器搜索归档库(Archve Libraries)时，它会提取包含未定义或暂定global symbol定义的归档成员。成员的定义可以是global symbol或weak symbol。默认情况下，链接编辑器不会提取归档成员来解析未定义的weak symbol。未解析的weak symbol将具有零值。使用 `-z weakextract` 选项可以覆盖此默认行为，从而使weak引用能够引发归档成员的提取。
		
	在每一个symbol table里， 所有Local的symbol binding 优先于global 和weak symbol。
		
**符号的类型。**

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/symbol_type.png)
	
> `STT_NOTYPE`: 没有类型
`STT_OBJECT`: 关联一个object， 比如variable， array 等等。
`STT_FUNC`: 关联一个function或者其他的可执行代码。
`STT_SECTION`: 关联一个section。主要是为了重定位， 通常有STB_LOCAL绑定属性。
`STT_FILE`: symbol的name 能够给出和object file 关联的source file的名字。FILE 符号具有STB_LOCAL属性， 它的section index是SHN_ABS。如果这个符号存在， 他会位于该文件的其他STB_LOCAL符号之前。SHT_SYMTAB的符号index 1 是一个表示文件本身的STT_FILE symbol。 通常，这个符号后面是文件的STT_SECTION 符号， 以及任何被降级为Local 符号的Global 符号。
`STT_COMMON`： 没有初始化的common的block， 它会被和STT_OBECT 完全同样对待。
`STT_LOOS` - `STT_HIOS`: OS-specific 预留的。
`STT_LOPROC` - `STT_HIPROC`: processor-specific 预留的。

如下code 展示如何操作这些值(sys/elf.h)
```
#define ELF32_ST_BIND(info)          ((info) >> 4)
#define ELF32_ST_TYPE(info)          ((info) & 0xf)
#define ELF32_ST_INFO(bind, type)    (((bind)<<4)+((type)&0xf))

#define ELF64_ST_BIND(info)          ((info) >> 4)
#define ELF64_ST_TYPE(info)          ((info) & 0xf)
#define ELF64_ST_INFO(bind, type)    (((bind)<<4)+((type)&0xf))
```

- **st_other**: 符号的可见性(Visibility)。

![enter image description here](https://github.com/xiaohuidu/debugging/blob/master/images/symbol_visibility.png)

> STV_DEFAULT: 符号的可见性右符号的binding 属性决定。glocal，weak，local。
STV_PROTECTED


如下code展示如何操作这些值.
```
#define ELF32_ST_VISIBILITY(o)       ((o)&0x3)
#define ELF64_ST_VISIBILITY(o)       ((o)&0x3)
```
- **st_shndx**:  每一个符号表entry 的定义都是和某一个section 相关联的。这个member 保存相关的section 在section header table 里的index。

## 


<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEwMjQxMjEyMSwtMTM0MDQ3Mjc3OSwtMj
AyNDQ2MzQzLC0xNTA3NzkyNjMyLDExMzMzNTUzOTAsLTIwNzM5
OTYzNzEsMTU5MDkzNjg1NSwxOTQwNDEzMDkxLC0xMzc4ODU4MT
YzLC0xNzQzNTEyNTQ5LDEyMzI4MzcwMTYsLTIwMDI3OTQ4MTUs
LTIwNzA1NzA4NDIsODg4Mzg4NTgxLC01Mzk3MzgzNzMsMjA0NT
E1NDQ5LC0xMDgyOTQ1MzU5LDU3MzE1ODM0MiwyMDI3NDg1Nywt
MTQxNzU1NTc0M119
-->