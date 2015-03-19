# lec6 SPOC思考题


NOTICE
- 有"w3l2"标记的题是助教要提交到学堂在线上的。
- 有"w3l2"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。


## 个人思考题
---

（1） (w3l2) 请简要分析64bit CPU体系结构下的分页机制是如何实现的
```
  + 采分点：说明64bit CPU架构的分页机制的大致特点和页表执行过程
  - 答案没有涉及如下3点；（0分）
  - 正确描述了64bit CPU支持的物理内存大小限制（1分）
  - 正确描述了64bit CPU下的多级页表的级数和多级页表的结构或反置页表的结构（2分）
  - 除上述两点外，进一步描述了在多级页表或反置页表下的虚拟地址-->物理地址的映射过程（3分）
 ```
- 64bit的cpu最多能支持的内存容量是2^64Byte，即16EiB。多级页表一共二~四级，每级存储了下一级页表的其实地址，最后一级存储了物理页帧号，加上虚拟地址中的页内偏移，即可得到物理地址。

>  

## 小组思考题
---

（1）(spoc) 某系统使用请求分页存储管理，若页在内存中，满足一个内存请求需要150ns (10^-9s)。若缺页率是10%，为使有效访问时间达到0.5us(10^-6s),求不在内存的页面的平均访问时间。请给出计算步骤。 

- 设缺页的平均访问时间为t (ns)，则
```
    90% * 150ns + 10% * t = 500
```
（2）(spoc) 有一台假想的计算机，页大小（page size）为32 Bytes，支持32KB的虚拟地址空间（virtual address space）,有4KB的物理内存空间（physical memory），采用二级页表，一个页目录项（page directory entry ，PDE）大小为1 Byte,一个页表项（page-table entries
PTEs）大小为1 Byte，1个页目录表大小为32 Bytes，1个页表大小为32 Bytes。页目录基址寄存器（page directory base register，PDBR）保存了页目录表的物理地址（按页对齐）。

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

int hex2dec(string s)
{
	int hi, lo;
	if (isdigit(s[0]))
		hi = s[0] - 48;
	else hi = s[0] - 97 + 10;
	if (isdigit(s[1]))
		lo = s[1] - 48;
	else lo = s[1] - 97 + 10;
	return hi * 16 + lo;	
}

int main()
{
	ifstream fin1("mem.in");
	ifstream fin2("a.in");

	string s;
	for (int i = 0; i < 128; i ++) 	//128 pages
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
	
	int PDBR = 544;
	while (fin2 >> s)
	{
		fin2 >> s;
		fin2 >> s;
		
		int va;
		stringstream sin2(s);	
		sin2 >> std::hex >> va;
		cout << "Virtual Address " << s << ":" << endl;
		
		//check page directory
		int PD_offset = va >> 10;
		int PD_valid = mem[PDBR + PD_offset] >> 7;
		int PD_pfn = mem[PDBR + PD_offset] & 127;
		
		cout << "  --> pde index: 0x" << std::hex << PD_offset << " pde contents: (valid " 
			<< PD_valid << ", pfn 0x" << std::hex << PD_pfn << ")" << endl;
		
		if (! PD_valid)
		{
			cout << "    --> Fault (page directory entry not valid)" << endl << endl;
			continue;
		}
		
		//check page table
		int PT_offset = (va >> 5) & 31;
		int PT_valid = mem[(PD_pfn << 5) + PT_offset] >> 7;
		int PT_pfn = mem[(PD_pfn << 5) + PT_offset] & 127;
		cout << "    --> pte index: 0x" << std::hex << PT_offset << " pde contents: (valid "
			<< PT_valid << ", pfn 0x" << std::hex << PT_pfn << ")" << endl;
		
		if (! PT_valid)
		{
			cout << "      --> Fault (page table entry not valid)" << endl << endl;
			continue;
		}
		
		int pa = (PT_pfn << 5) + (va & 31);
		cout << "      --> Translates to Physical Address 0x" << std::hex << pa << " --> Value 0x" << std::hex << mem[pa] << endl;
		cout << endl;
	}
	
	system("pause");
	return 0;
}
			
```

- 运行结果为：

```
Virtual Address 6c74:
  --> pde index: 0x1b pde contents: (valid 1, pfn 0x20)
    --> pte index: 0x3 pde contents: (valid 1, pfn 0x61)
      --> Translates to Physical Address 0xc34 --> Value 0x6

Virtual Address 6b22:
  --> pde index: 0x1a pde contents: (valid 1, pfn 0x52)
    --> pte index: 0x19 pde contents: (valid 1, pfn 0x47)
      --> Translates to Physical Address 0x8e2 --> Value 0x1a

Virtual Address 03df:
  --> pde index: 0x0 pde contents: (valid 1, pfn 0x5a)
    --> pte index: 0x1e pde contents: (valid 1, pfn 0x5)
      --> Translates to Physical Address 0xbf --> Value 0xf

Virtual Address 69dc:
  --> pde index: 0x1a pde contents: (valid 1, pfn 0x52)
    --> pte index: 0xe pde contents: (valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)

Virtual Address 317a:
  --> pde index: 0xc pde contents: (valid 1, pfn 0x18)
    --> pte index: 0xb pde contents: (valid 1, pfn 0x35)
      --> Translates to Physical Address 0x6ba --> Value 0x1e

Virtual Address 4546:
  --> pde index: 0x11 pde contents: (valid 1, pfn 0x21)
    --> pte index: 0xa pde contents: (valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)

Virtual Address 2c03:
  --> pde index: 0xb pde contents: (valid 1, pfn 0x44)
    --> pte index: 0x0 pde contents: (valid 1, pfn 0x57)
      --> Translates to Physical Address 0xae3 --> Value 0x16

Virtual Address 7fd7:
  --> pde index: 0x1f pde contents: (valid 1, pfn 0x12)
    --> pte index: 0x1e pde contents: (valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)

Virtual Address 390e:
  --> pde index: 0xe pde contents: (valid 0, pfn 0x7f)
    --> Fault (page directory entry not valid)

Virtual Address 748b:
  --> pde index: 0x1d pde contents: (valid 1, pfn 0x0)
    --> pte index: 0x4 pde contents: (valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)
```
			
		




（3）请基于你对原理课二级页表的理解，并参考Lab2建页表的过程，设计一个应用程序（可基于python, ruby, C, C++，LISP等）可模拟实现(2)题中描述的抽象OS，可正确完成二级页表转换。


（4）假设你有一台支持[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)的机器，请问你如何设计操作系统支持这种类型计算机？请给出设计方案。

 (5)[X86的页面结构](http://os.cs.tsinghua.edu.cn/oscourse/OS2015/lecture06#head-1f58ea81c046bd27b196ea2c366d0a2063b304ab)
--- 

## 扩展思考题

阅读64bit IBM Powerpc CPU架构是如何实现[反置页表](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)，给出分析报告。

--- 
