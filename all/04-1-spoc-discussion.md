#lec8 虚拟内存spoc练习


NOTICE
- 有"w4l2"标记的题是助教要提交到学堂在线上的。
- 有"w4l2"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。


## 个人思考题

### 内存访问局部性的应用程序例子
---
(1)(w4l2)下面是一个体现内存访问局部性好的简单应用程序例子，请参考，在linux中写一个简单应用程序，体现内存局部性差，并给出其执行时间。
```
#include <stdio.h>
#define NUM 1024
#define COUNT 10
int A[NUM][NUM];
void main (void) {
  int i,j,k;
  for (k = 0; k<COUNT; k++)
  for (i = 0; i < NUM; i++)
  for (j = 0; j	 < NUM; j++)
      A[i][j] = i+j;
  printf("%d count computing over!\n",i*j*k);
}
```
可以用下的命令来编译和运行此程序：
```
gcc -O0 -o goodlocality goodlocality.c
time ./goodlocality
```
可以看到其执行时间。
- 原程序的执行时间为：
```
real    0m0.042s
user    0m0.040s
sys     0m0.002s
```
将三重循环中的`i`和`j`调换，则程序的执行时间为：
```
real    0m0.180s
user    0m0.178s
sys     0m0.002s
```
可以看见，改变循环后的程序，局部性变差，运行速度变慢了将近4倍。

## 小组思考题目
----

### 缺页异常嵌套

（1）缺页异常可用于虚拟内存管理中。如果在中断服务例程中进行缺页异常的处理时，再次出现缺页异常，这时计算机系统（软件或硬件）会如何处理？请给出你的合理设计和解释。

### 缺页中断次数计算
（2）如果80386机器的一条机器指令(指字长4个字节)，其功能是把一个32位字的数据装入寄存器，指令本身包含了要装入的字所在的32位地址。这个过程最多会引起几次缺页中断？
> 提示：内存中的指令和数据的地址需要考虑地址对齐和不对齐两种情况。需要考虑页目录表项invalid、页表项invalid、TLB缺失等是否会产生中断？

### 虚拟页式存储的地址转换

（3）(spoc) 有一台假想的计算机，页大小（page size）为32 Bytes，支持8KB的虚拟地址空间（virtual address space）,有4KB的物理内存空间（physical memory），采用二级页表，一个页目录项（page directory entry ，PDE）大小为1 Byte,一个页表项（page-table entries
PTEs）大小为1 Byte，1个页目录表大小为32 Bytes，1个页表大小为32 Bytes。页目录基址寄存器（page directory base register，PDBR）保存了页目录表的物理地址（按页对齐）。

PTE格式（8 bit） :
```
  VALID | PFN6 ... PFN0
```
PDE格式（8 bit） :
```
  VALID | PT6 ... PT0
```
其
```
VALID==1表示，表示映射存在；VALID==0表示，表示内存映射不存在（有两种情况：a.对应的物理页帧swap out在硬盘上；b.既没有在内存中，页没有在硬盘上，这时页帧号为0x7F）。
PFN6..0:页帧号或外存中的后备页号
PT6..0:页表的物理基址>>5
```

已经建立好了1个页目录表和8个页表，且页目录表的index为0~7的页目录项分别对应了这8个页表。

在[物理内存模拟数据文件](./04-1-spoc-memdiskdata.md)中，给出了4KB物理内存空间和4KBdisk空间的值，PDBR的值。

请回答下列虚地址是否有合法对应的物理内存，请给出对应的pde index, pde contents, pte index, pte contents，the value of addr in phy page OR disk sector。
```
Virtual Address 6653:
Virtual Address 1c13:
Virtual Address 6890:
Virtual Address 0af6:
Virtual Address 1e6f:
```
- 编写如下程序：
```
#include <algorithm>
#include <iostream>
#include <cstring>
#include <fstream>
#include <sstream>
#include <vector>
#include <string>
#include <set>
#include <map>
using namespace std;


int mem[4096];
int disk[4096];


int main()
{
    ifstream fin1("mem.in");
    ifstream fin2("disk.in");
    ifstream fin3("a.in");
    freopen("a.out", "w", stdout);

    string s;
    for (int i = 0; i < 128; i ++)  //128 pages
    {
        fin1 >> s; // page 
        fin1 >> s; // xx:

        for (int j = 0; j < 32; j ++)
        {
            fin1 >> s;
            stringstream sin1(s);
            sin1 >> std::hex >> mem[i * 32 + j];
        }
    }

    for (int i = 0; i < 128; i ++)  //128 disk sectors
    {
        fin2 >> s; // page 
        fin2 >> s; // xx:

        for (int j = 0; j < 32; j ++)
        {
            fin2 >> s;
            stringstream sin2(s);
            sin2 >> std::hex >> disk[i * 32 + j];
        }
    }

    int PDBR = 3456;
    while (fin3 >> s)
    {
        fin3 >> s;
        fin3 >> s;

        int va;
        stringstream sin3(s);   
        sin3 >> std::hex >> va;
        cout << "Virtual Address " << s << ":" << endl;

		int pde_index = va >> 10;
		int pde_content = mem[PDBR + pde_index];
		int pde_valid = (pde_content & 128) >> 7;
		int pt_pa = pde_content & 127;

		cout << "  --> pde index: 0x" << std::hex << pde_index << "  pde contents: (valid " 
			<< pde_valid << ", pt 0x" << std::hex << pt_pa << ")" << endl;
		
		if (! pde_valid)
		{
			cout << "    --> PDE invalid" << endl << endl;
			continue;
		}
		
		int pte_index = (va >> 5) & 31;
		int pte_content = mem[(pt_pa << 5) + pte_index];
		int pte_valid = (pte_content & 128) >> 7;
		int pfn = pte_content & 127;
		
		cout << "    --> pte index: 0x" << std::hex << pte_index << "  pte contents: (valid " 
			<< pte_valid << ", pfn 0x" << std::hex << pfn << ")" << endl;
		
		if (! pte_valid)
		{
			if (pfn == 127)
				cout << "      --> This page is neither in memory nor on disk." << endl << endl;
			else
			{
				int disk_addr = (pfn << 5) + (va & 31);
				int disk_content = disk[disk_addr];
				cout << "      --> To Disk Sector Address 0x" << std::hex << disk_addr 
					<< " --> Value: " << std::hex << disk_content << endl << endl;
			}
			continue;
		}
		
		int pa = (pfn << 5) + (va & 31);
		int pa_content = mem[pa];
		cout << "      --> To Physical Address 0x" << std::hex << pa 
			<< " --> Value: " << std::hex << pa_content << endl << endl;
	}
	return 0;
}
```
- 运行结果为：
```
Virtual Address 0330:
  --> pde index: 0x0  pde contents: (valid 1, pt 0x61)
    --> pte index: 0x19  pte contents: (valid 1, pfn 0x63)
      --> To Physical Address 0xc70 --> Value: 2

Virtual Address 1e6f:
  --> pde index: 0x7  pde contents: (valid 1, pt 0x3d)
    --> pte index: 0x13  pte contents: (valid 0, pfn 0x16)
      --> To Disk Sector Address 0x2cf --> Value: 1c

Virtual Address 6653:
  --> pde index: 0x19  pde contents: (valid 0, pt 0x7f)
    --> PDE invalid

Virtual Address 1c13:
  --> pde index: 0x7  pde contents: (valid 1, pt 0x3d)
    --> pte index: 0x0  pte contents: (valid 1, pfn 0x76)
      --> To Physical Address 0xed3 --> Value: 12

Virtual Address 6890:
  --> pde index: 0x1a  pde contents: (valid 0, pt 0x7f)
    --> PDE invalid

Virtual Address 0af6:
  --> pde index: 0x2  pde contents: (valid 1, pt 0x21)
    --> pte index: 0x17  pte contents: (valid 0, pfn 0x7f)
      --> This page is neither in memory nor on disk.

Virtual Address 1e6f:
  --> pde index: 0x7  pde contents: (valid 1, pt 0x3d)
    --> pte index: 0x13  pte contents: (valid 0, pfn 0x16)
      --> To Disk Sector Address 0x2cf --> Value: 1c
```

## 扩展思考题
---
(1)请分析原理课的缺页异常的处理流程与lab3中的缺页异常的处理流程（分析粒度到函数级别）的异同之处。

(2)在X86-32虚拟页式存储系统中，假定第一级页表的起始地址是0xE8A3 B000，进程地址空间只有第一级页表的4KB在内存。请问这4KB的虚拟地址是多少？它对应的第一级页表项和第二级页表项的物理地址是多少？页表项的内容是什么？
