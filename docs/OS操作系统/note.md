# 操作系统课程设计

---

## lab1_challenge1

---

!!! abstract "任务目的"
    将用户程序函数调用关系通过'print_backtrace'回溯打印出来

### 需要完善的内容

- 系统调用路径

> 通过ecall指令完成系统调用的，且在执行ecall指令前，所有的参数（即do_user_call函数的8个参数）实际上都已经载入到RISC-V机器的a0到a7，这8个寄存器中（这一步是我们的编译器生成的代码帮我们完成的）。ecall指令的执行将根据a0寄存器中的值获得系统调用号，并使RISC-V转到S模式（因为我们的操作系统内核启动时将所有的中断、异常、系统调用都代理给了S模式）的trap处理入口执行（在kernel/strap_vector.S文件中定义）

- 在操作系统内核中获取用户程序的栈，需要在陷入到内核时找到用户进程的用户态栈

> 通过fp(process.h里的)追踪栈底
函数调用时栈帧的结构，通过fp寄存器(s0)寻找各个栈帧

- 找到用户态栈后，通过用户态栈的结构进行打印操作

>读取出.symtab和.strtab的信息之后就可以开始打印

- 再通过用户栈找到函数的返回地址，需要将虚拟地址转换为源程序中的符号。因此需要了解elf文件中的符号节(.symTab section)(symTab段是ELF中存储符号表的段，主要用于编译链接，也可以参与动态库的加载)以及字符串节(.strTab section)的相关知识。通过两个节里存储的内容以及存储的格式等内容。

### Elf文件

- elf文件

>基于Linking view的方式：根据Section Header Table解析Section，这个时候按照基于文件的偏移量来读取不同的Section的内容，再根据Section中对应的数据来解析。
基于Execution view的方式，先打开文件，然后逐个将PH Table中的segment通过m-map映射到对应的虚拟内存空间，然后遍历segment的内容。

- 一个 ELF 头在文件的开始，保存了路线图(road map)，描述了该文件的组织情况。可以说， ELF 头是整个ELF文件的“灵魂”所在

- ELF头存储的内容:shoff记录了第一个Section Header的位置，shentsize代表了一个Section Header的大小，shnum代表一共有多少个Section Header，shstrndx代表了String Table的索引值

???+ note "ELF Header 和 Section Header"
    === "ELF Header 的结构"
        ```c title="elf header"
        typedef struct elf_header_t {
          uint32 magic;
          uint8 elf[12];
          uint16 type;      /* Object file type */
          uint16 machine;   /* Architecture */
          uint32 version;   /* Object file version */
          uint64 entry;     /* Entry point virtual address */
          uint64 phoff;     /* Program header table file offset */
          uint64 shoff;     /* Section header table file offset */
          uint32 flags;     /* Processor-specific flags */
          uint16 ehsize;    /* ELF header size in bytes */
          uint16 phentsize; /* Program header table entry size */
          uint16 phnum;     /* Program header table entry count */
          uint16 shentsize; /* Section header table entry size */
          uint16 shnum;     /* Section header table entry count */
          uint16 shstrndx;  /* Section header string table index */
        } elf_header;
        ```
    === "Section Header 的结构"
        ```c title="Section Header"
        typedef struct{  /*Section Header 的结构*/
            Elf32_Word sh_name; /*Section name (string tbl index)*/  
            Elf32_Word sh_type; /*Section type*/  
            Elf32_Word sh_flags; /*Section flags*/  
            Elf32_Addr sh_addr; /*Section virtual addr at execution*/  
            Elf32_Off sh_offset; /*Section file offset*/  
            Elf32_Word sh_size; /*Section size in bytes*/  
            Elf32_Word sh_link; /*Link to another section*/  
            Elf32_Word sh_info; /*Additional section information*/  
            Elf32_Word sh_addralign; /*Section alignment*/  
            Elf32_Word sh_entsize; /*Entry size if section holds table*/
            }
            Elf32_Shdr;
        ```
    === "section header type 取值"
        ```c title="sh_type"
        0 SHT_NULL 无对应节区，该节其他字段取值无意义
        1 SHT_PROGBITS 程序数据
        2 SHT_SYMTAB 符号表
        3 SHT_STRTAB 字符串表
        4 SHT_RELA 带附加的重定位项
        5 SHT_HASH 符号哈希表
        6 SHT_DYNAMIC 动态链接信息
        7 SHT_NOTE 提示性信息
        8 SHT_NOBITS 无数据程序空间（bss）
        9 SHT_REL 无附加的重定位项
        10 SHT_SHLIB 保留
        11 SHT_DYNSYM 动态链接符号表
        14 SHT_INIT_ARRAY 构造函数数组
        15 SHT_FINI_ARRAY 析构函数数组
        16 SHT_PREINIT_ARRAY
        17 SHT_GROUP
        18 SHT_SYMTAB_SHNDX
        19 SHT_NUM
        0x70000000 SHT_LOPROC
        0x7fffffff SHT_HIPROC
        0x80000000 SHT_LOUSER
        0x8fffffff SHT_HIUSER
        ```
    === "section header flags取值"
        ```c title="sh_flags"
        1 SHF_WRITE
        2 SHF_ALLOC
        4 SHF_EXECINSTR
        8 SHF_MASKPROC
        ```

- 通过String Table可以读取出每个Section Header对应的Section的名字，通过C语言的字符串比较函数，可以读取我们想要的Section的内容。
- 对于.symtab和.strtab，需要查询.symtab表中每一个symbols的结构定义，其中的name也是一个uint32类型的变量
- 读取出.symtab和.strtab的信息之后就可以开始打印了，在进入中断之前其实所有的信息已经进入到了trapframe里面，所以说可以读取trapframe里面的信息读取某个寄存器在进入S态之前的值。
- 下面这个函数从offset这个地址中取出nb字节的数据放入到dest对应的地址中。

```C
uint64 elf_fpread(elf_ctx *ctx, void *dest, uint64 nb, uint64 offset)
```

### 实验过程

!!! note "获取elf"
    === "ctx-info"
        ??? note "info define"
            ```c
            typedef struct elf_info_t {
              spike_file_t *f;
              process *p;
            } elf_info;
            ```
        ```c
        ///ctx:800065f8
        Try print ctx->info:0x800065e8
        Try print dest:80006600
        Try print nb:64
        Try print offset:0
        ---
        Try print ctx->info:0x800065e8
        Try print dest:80006568
        Try print nb:56
        Try print offset:64
        ---
        Try print ctx->info:0x800065e8
        Try print dest:81000000
        Try print nb:1052
        Try print offset:4096
        ```
    === "获取msg->f"
        ??? note "msg"
            ```c title="msg define"
            elf_info *msg = (elf_info *)ctx->info;
            ```
            ```c title="process define"
            // the extremely simple definition of process, used for begining labs of PKE
            typedef struct process_t {
              // pointing to the stack used in trap handling.
              uint64 kstack;
              // trapframe storing the context of a (User mode) process.
              trapframe* trapframe;
            }process;
            ```
        ```c
        Try print msg->f:0x80005018
        Try print dest:80006600
        Try print nb:64
        Try print offset:0

        Try print msg->f:0x80005018
        Try print dest:80006568
        Try print nb:56
        Try print offset:64

        Try print msg->f:0x80005018
        Try print dest:81000000
        Try print nb:1052
        Try print offset:4096
        ```
    === "获取magic"
        ```c
        Try print ctx->ehdr.magic:0x80006690
        Try print dest:80006600
        Try print nb:64
        Try print offset:0

        Try print ctx->ehdr.magic:0x464c457f
        Try print dest:80006568
        Try print nb:56
        Try print offset:64

        Try print ctx->ehdr.magic:0x464c457f
        Try print dest:81000000
        Try print nb:1052
        Try print offset:4096
        ```
    === "获取type"
        ```c title="Object file type"
        Try print ctx->header->type:5132
        Try print dest:-2147457536
        Try print nb:64
        Try print offset:0

        Try print ctx->header->type:2
        Try print dest:-2147457688
        Try print nb:56
        Try print offset:64

        Try print ctx->header->type:2
        Try print dest:-2130706432
        Try print nb:1052
        Try print offset:4096
        ```
    === "获取shoff"
        ```c title="Section header table file offset"
        Try print ctx->ehdr.shoff:0x800066a8
        Try print dest:80006600
        Try print nb:64
        Try print offset:0
        
        Try print ctx->ehdr.shoff:0x00003de0(15840)
        Try print dest:80006568
        Try print nb:56
        Try print offset:64
        
        Try print ctx->ehdr.shoff:0x00003de0(15840)
        Try print dest:81000000
        Try print nb:1052
        Try print offset:4096
        ```
    === "获取shentsize"
        ```c title="Section header table entry size"
        Try print ctx->ehdr.shentsize:0
        Try print dest:-2147457536
        Try print nb:64
        Try print offset:0

        Try print ctx->ehdr.shentsize:64
        Try print dest:-2147457688
        Try print nb:56
        Try print offset:64

        Try print ctx->ehdr.shentsize:64
        Try print dest:-2130706432
        Try print nb:1052
        Try print offset:4096
        ```
    === "获取shnum"
        ```c title="Section header table entry count"
        Try print ctx->ehdr.shnum:1
        Try print dest:-2147457536
        Try print nb:64
        Try print offset:0

        Try print ctx->ehdr.shnum:17
        Try print dest:-2147457688
        Try print nb:56
        Try print offset:64

        Try print ctx->ehdr.shnum:17
        Try print dest:-2130706432
        Try print nb:1052
        Try print offset:4096
        ```
    === "获取shstrndx"
        ```c title="Section header string table index(String Table的索引值)"
        Try print ctx->ehdr.shstrndx:0
        Try print dest:-2147457536
        Try print nb:64
        Try print offset:0

        Try print ctx->ehdr.shstrndx:16
        Try print dest:-2147457688
        Try print nb:56
        Try print offset:64

        Try print ctx->ehdr.shstrndx:16
        Try print dest:-2130706432
        Try print nb:1052
        Try print offset:4096
        ```
!!! warning "ToDo"
    找到section header！

## lab1_challenge2

!!! note "任务目的"
    修改内核（包括machine文件夹下）的代码，使得用户程序在发生异常时，内核能够输出触发异常的用户程序的源文件名和对应代码行

### 待完善的内容

>1. 本实验在```elf.c```中给出了```debug_line```段的解析函数*```make_addr_line```*。
>
>2. 这个函数接受三个参数，```ctx```为```elf```文件的上下文指针，这个可以参考文件中的其他函数
>
>3. ```debug_line```为指向```.debug_line```段数据的指针
>
> 你需要读取```elf```文件中名为```.debug_line```的段保存到缓冲区中
>
> 然后将缓冲区指针传入这个参数；```length```为```.debug_line```段数据的长度。
