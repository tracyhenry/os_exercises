# lab2 SPOC思考题

NOTICE
- 有"w4l1"标记的题是助教要提交到学堂在线上的。
- 有"w4l1"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。

## 个人思考题
---

x86保护模式中权限管理无处不在，下面哪些时候要检查访问权限()  (w4l1)
- [x] 内存寻址过程中
- [x] 代码跳转过程中
- [x] 中断处理过程中
- [ ] ALU计算过程中
 
> 前三个需要。这里假定ALU完成计算所需数据都已经在CPU内部了。


请描述ucore OS建立页机制的准备工作包括哪些步骤？ (w4l1) 
```
  + 采分点：说明了ucore OS在让页机制正常工作的主要准备工作
  - 答案没有涉及如下3点；（0分）
  - 描述了对GDT的初始化,完成了段机制（1分）
  - 除第二点外进一步描述了对物理内存的探测和空闲物理内存的管理。（2分）
  - 除上述两点外，进一步描述了页表建立初始过程和设置CR0控寄存器某位来使能页（3分）

 ```
- 首先在bootloader中完成了GDT的初始化，建立段机制 
- 其次对物理内存空间进行侦测，初始化空闲物理内存的管理
- 建立二级页表，先建立Page Directory，其次对每个页目录项建立页表
- 设置CR0寄存器最高位，使能页表，之后再初始化GDT适应页机制

>  

---

## 小组思考题
---

（1）（spoc）请用lab1实验的基准代码（即没有修改的需要填空的源代码）来做如下实验： 执行`make qemu`，会得到一个输出结果，请给出合理的解释：为何qemu退出了？【提示】需要对qemu增加一些用于基于执行过的参数，重点是分析其执行的指令和产生的中断或异常。 

- 因为没有修改的代码没有建立idt，而make qemu需要响应时钟中断

> 

（2）(spoc)假定你已经完成了lab1的实验,接下来是对lab1的中断处理的回顾：请把你的学号对37(十进制)取模，得到一个数x（x的范围是-1<x<37），然后在你的答案的基础上，修init.c中的kern_init函数，在大约36行处，即

```
    intr_enable();              // enable irq interrupt
```
语句之后，加入如下语句(把x替换为你学号 mod 37得的值)：
```
    asm volatile ("int $x");
```    
然后，请回答加入这条语句后，执行`make qemu`的输出结果与你没有加入这条语句后执行`make qemu`的输出结果的差异，并解释为什么有差异或没差异？ 

- 有差异，程序输出：
```
trapframe at 0x7b74
  edi  0x00000000
  esi  0x00010094
  ebp  0x00007be8
  oesp 0x00007b94
  ebx  0x00010094
  edx  0x000000a1
  ecx  0x00000000
  eax  0x000000ff
  ds   0x----0010
  es   0x----0010
  fs   0x----0023
  gs   0x----0023
  trap 0x0000000d General Protection
  err  0x00001368
  eip  0x00101e8b
  cs   0x----0008
  flag 0x00000006 PF,IOPL=0
kernel panic at kern/trap/trap.c:210:
    unexpected trap in kernel.

Welcome to the kernel debug monitor!!
Type 'help' for a list of commands.
K> help
help - Display this list of commands.
kerninfo - Display information about the kernel.
backtrace - Print backtrace of stack frame.
```
原因在于这是ucore lab1无法识别的中断类型，对应于trap.c的206行左右的default case标签。

> 

（3）对于lab2的输出信息，请说明数字的含义
```
e820map:
  memory: 0009fc00, [00000000, 0009fbff], type = 1.
  memory: 00000400, [0009fc00, 0009ffff], type = 2.
  memory: 00010000, [000f0000, 000fffff], type = 2.
  memory: 07ee0000, [00100000, 07fdffff], type = 1.
  memory: 00020000, [07fe0000, 07ffffff], type = 2.
  memory: 00040000, [fffc0000, ffffffff], type = 2.
```
修改lab2，让其显示` type="some string"` 让人能够读懂，而不是不好理解的数字1,2  (easy) 
- [x]  

> 

（4）(spoc)有一台只有页机制的简化80386的32bit计算机，有地址范围位0~256MB的物理内存空间（physical memory），可表示大小为256MB，范围为0xC0000000~0xD0000000的虚拟地址空间（virtual address space）,页大小（page size）为4KB，采用二级页表，一个页目录项（page directory entry ，PDE）大小为4B,一个页表项（page-table entries PTEs）大小为4B，1个页目录表大小为4KB，1个页表大小为4KB。
```
PTE格式（32 bit） :
  PFN19 ... PFN0|NOUSE9 ... NOUSE0|WRITABLE|VALID
PDE格式（32 bit） :
  PT19 ... PT0|NOUSE9 ... NOUSE0|WRITABLE|VALID
 
其中：
NOUSE9 ... NOUSE0为保留位，要求固定为0
WRITABLE：1表示可写，0表示只读
VLAID：1表示有效，0表示无效
```

假设ucore OS已经为此机器设置好了针对如下虚拟地址<-->物理地址映射的二级页表，设置了页目录基址寄存器（page directory base register，PDBR）保存了页目录表的物理地址（按页对齐），其值为0。已经建立好了从物理地址0x1000~0x41000的二级页表，且页目录表的index为0x300~0x363的页目录项的(PT19 ... PT0)的值=(index-0x300+1)。
请写出一个translation程序（可基于python, ruby, C, C++，LISP等），输入是一个虚拟地址和一个物理地址，能够自动计算出对应的页目录项的index值,页目录项内容的值，页表项的index值，页表项内容的值。即(pde_idx, pde_ctx, pte_idx, pte_cxt)

请用如下值来验证你写的程序的正确性：
```
va 0xc2265b1f, pa 0x0d8f1b1f
va 0xcc386bbc, pa 0x0414cbbc
va 0xc7ed4d57, pa 0x07311d57
va 0xca6cecc0, pa 0x0c9e9cc0
va 0xc18072e8, pa 0x007412e8
va 0xcd5f4b3a, pa 0x06ec9b3a
va 0xcc324c99, pa 0x0008ac99
va 0xc7204e52, pa 0x0b8b6e52
va 0xc3a90293, pa 0x0f1fd293
va 0xce6c3f32, pa 0x007d4f32
```

- 编写程序如下：
```
string s1, s2;	
unsigned int va, pa;
while (cin >> s1 >> s2)
{
	stringstream sin1(s1);
	stringstream sin2(s2);
	sin1 >> std::hex >> va;
	sin2 >> std::hex >> pa;
	
	cout << "va 0x" << std::hex << va << ", pa 0x" << std::hex << pa << ", ";
	
	unsigned int pde_idx = va >> 22;
	cout << "pde_idx 0x" << std::hex << pde_idx << ", ";
	
	unsigned int pde_ctx = ((pde_idx - 3 * 256 + 1) << 12) + 3;
	cout << "pde_ctx 0x" << std::hex << pde_ctx << ", ";
	
	unsigned int pte_idx = (va >> 12) & 1023;
	cout << "pte_idx 0x" << std::hex << pte_idx << ", ";
	
	unsigned int pte_ctx = ((pa >> 12) << 12) + 3;
	cout << "pte_ctx 0x" << std::hex << pte_ctx << endl; 
}
```
运行结果为：
```
va 0xcd82c07c, pa 0xc20907c, pde_idx 0x336, pde_ctx 0x37003, pte_idx 0x2c, pte_ctx 0xc209003
va 0xc2265b1f, pa 0xd8f1b1f, pde_idx 0x308, pde_ctx 0x9003, pte_idx 0x265, pte_ctx 0xd8f1003
va 0xcc386bbc, pa 0x414cbbc, pde_idx 0x330, pde_ctx 0x31003, pte_idx 0x386, pte_ctx 0x414c003
va 0xc7ed4d57, pa 0x7311d57, pde_idx 0x31f, pde_ctx 0x20003, pte_idx 0x2d4, pte_ctx 0x7311003
va 0xca6cecc0, pa 0xc9e9cc0, pde_idx 0x329, pde_ctx 0x2a003, pte_idx 0x2ce, pte_ctx 0xc9e9003
va 0xc18072e8, pa 0x7412e8, pde_idx 0x306, pde_ctx 0x7003, pte_idx 0x7, pte_ctx 0x741003
va 0xcd5f4b3a, pa 0x6ec9b3a, pde_idx 0x335, pde_ctx 0x36003, pte_idx 0x1f4, pte_ctx 0x6ec9003
va 0xcc324c99, pa 0x8ac99, pde_idx 0x330, pde_ctx 0x31003, pte_idx 0x324, pte_ctx 0x8a003
va 0xc7204e52, pa 0xb8b6e52, pde_idx 0x31c, pde_ctx 0x1d003, pte_idx 0x204, pte_ctx 0xb8b6003
va 0xc3a90293, pa 0xf1fd293, pde_idx 0x30e, pde_ctx 0xf003, pte_idx 0x290, pte_ctx 0xf1fd003
va 0xce6c3f32, pa 0x7d4f32, pde_idx 0x339, pde_ctx 0x3a003, pte_idx 0x2c3, pte_ctx 0x7d4003
```

---

## 开放思考题

---

（1）请简要分析Intel的x64 64bit体系结构下的分页机制是如何实现的 
```
  + 采分点：说明Intel x64架构的分页机制的大致特点和页表执行过程
  - 答案没有涉及如下3点；（0分）
  - 正确描述了x64支持的物理内存大小限制（1分）
  - 正确描述了x64下的多级页表的级数和多级页表的结构（2分）
  - 除上述两点外，进一步描述了在多级页表下的虚拟地址-->物理地址的映射过程（3分）
 ```
- [x]  

>  

（2）Intel8086不支持页机制，但有hacker设计过包含未做任何改动的8086CPU的分页系统。猜想一下，hacker是如何做到这一点的？提示：想想MMU的逻辑位置

- [x]  

> 

---
