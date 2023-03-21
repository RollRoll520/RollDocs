# 操作系统课程设计

---

## lab1_challenge1

---

> 任务目的：将用户程序函数调用关系通过'print_backtrace'回溯打印出来

需要完善的内容

- 系统调用路径

> 通过ecall指令完成系统调用的，且在执行ecall指令前，所有的参数（即do_user_call函数的8个参数）实际上都已经载入到RISC-V机器的a0到a7，这8个寄存器中（这一步是我们的编译器生成的代码帮我们完成的）。ecall指令的执行将根据a0寄存器中的值获得系统调用号，并使RISC-V转到S模式（因为我们的操作系统内核启动时将所有的中断、异常、系统调用都代理给了S模式）的trap处理入口执行（在kernel/strap_vector.S文件中定义）

- 在操作系统内核中获取用户程序的栈，需要在陷入到内核时找到用户进程的用户态栈

> 通过fp(process.h里的)追踪栈底
函数调用时栈帧的结构，通过fp寄存器(s0)寻找各个栈帧

- 找到用户态栈后，通过用户态栈的结构进行打印操作

>读取出.symtab和.strtab的信息之后就可以开始打印

- 再通过用户栈找到函数的返回地址，需要将虚拟地址转换为源程序中的符号。因此需要了解elf文件中的符号节(.symTab section)(symTab段是ELF中存储符号表的段，主要用于编译链接，也可以参与动态库的加载)以及字符串节(.strTab section)的相关知识。通过两个节里存储的内容以及存储的格式等内容。

Tips

- elf文件

>基于Linking view的方式：根据Section Header Table解析Section，这个时候按照基于文件的偏移量来读取不同的Section的内容，再根据Section中对应的数据来解析。
基于Execution view的方式，先打开文件，然后逐个将PH Table中的segment通过m-map映射到对应的虚拟内存空间，然后遍历segment的内容。

- 一个 ELF 头在文件的开始，保存了路线图(road map)，描述了该文件的组织情况。可以说， ELF 头是整个ELF文件的“灵魂”所在

- ELF头存储的内容:shoff记录了第一个Section Header的位置，shentsize代表了一个Section Header的大小，shnum代表一共有多少个Section Header，shstrndx代表了String Table的索引值

```c
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

- 通过String Table可以读取出每个Section Header对应的Section的名字，通过C语言的字符串比较函数，可以读取我们想要的Section的内容。
- 对于.symtab和.strtab，需要查询.symtab表中每一个symbols的结构定义，其中的name也是一个uint32类型的变量
- 读取出.symtab和.strtab的信息之后就可以开始打印了，在进入中断之前其实所有的信息已经进入到了trapframe里面，所以说可以读取trapframe里面的信息读取某个寄存器在进入S态之前的值。
- 下面这个函数从offset这个地址中取出nb字节的数据放入到dest对应的地址中。

```C
uint64 elf_fpread(elf_ctx *ctx, void *dest, uint64 nb, uint64 offset)
```
