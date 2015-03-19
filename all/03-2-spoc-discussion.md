# lec6 SPOC˼����


NOTICE
- ��"w3l2"��ǵ���������Ҫ�ύ��ѧ�������ϵġ�
- ��"w3l2"��"spoc"��ǵ�����Ҫ�����廪ѧ�ֵ�ͬѧҪ��ʵ�������ɣ�����ʱ�ύ��ѧ����Ӧ��git repo�ϡ�
- ��"hard"��ǵ�����һ���Ѷȣ�����ʵ�֡�
- ��"easy"��ǵ��������ʵ�֣�����ʵ�֡�
- ��"midd"��ǵ�����һ��ˮƽ������ʵ�֡�


## ����˼����
---

��1�� (w3l2) ���Ҫ����64bit CPU��ϵ�ṹ�µķ�ҳ���������ʵ�ֵ�
```
  + �ɷֵ㣺˵��64bit CPU�ܹ��ķ�ҳ���ƵĴ����ص��ҳ��ִ�й���
  - ��û���漰����3�㣻��0�֣�
  - ��ȷ������64bit CPU֧�ֵ������ڴ��С���ƣ�1�֣�
  - ��ȷ������64bit CPU�µĶ༶ҳ��ļ����Ͷ༶ҳ��Ľṹ����ҳ��Ľṹ��2�֣�
  - �����������⣬��һ���������ڶ༶ҳ�����ҳ���µ������ַ-->�����ַ��ӳ����̣�3�֣�
 ```
- [x]  

>  

## С��˼����
---

��1��(spoc) ĳϵͳʹ�������ҳ�洢������ҳ���ڴ��У�����һ���ڴ�������Ҫ150ns (10^-9s)����ȱҳ����10%��Ϊʹ��Ч����ʱ��ﵽ0.5us(10^-6s),�����ڴ��ҳ���ƽ������ʱ�䡣��������㲽�衣 

- [x]  

> 500=0.9\*150+0.1\*x

��2��(spoc) ��һ̨����ļ������ҳ��С��page size��Ϊ32 Bytes��֧��32KB�������ַ�ռ䣨virtual address space��,��4KB�������ڴ�ռ䣨physical memory�������ö���ҳ��һ��ҳĿ¼�page directory entry ��PDE����СΪ1 Byte,һ��ҳ���page-table entries
PTEs����СΪ1 Byte��1��ҳĿ¼���СΪ32 Bytes��1��ҳ���СΪ32 Bytes��ҳĿ¼��ַ�Ĵ�����page directory base register��PDBR��������ҳĿ¼��������ַ����ҳ���룩��

PTE��ʽ��8 bit�� :
```
  VALID | PFN6 ... PFN0
```
PDE��ʽ��8 bit�� :
```
  VALID | PT6 ... PT0
```
��
```
VALID==1��ʾ����ʾӳ����ڣ�VALID==0��ʾ����ʾӳ�䲻���ڡ�
PFN6..0:ҳ֡��
PT6..0:ҳ��������ַ>>5
```
��[�����ڴ�ģ�������ļ�](./03-2-spoc-testdata.md)�У�������4KB�����ڴ�ռ��ֵ����ش��������ַ�Ƿ��кϷ���Ӧ�������ڴ棬�������Ӧ��pde index, pde contents, pte index, pte contents��
```
Virtual Address 6c74
Virtual Address 6b22
Virtual Address 03df
Virtual Address 69dc
Virtual Address 317a
Virtual Address 4546
Virtual Address 2c03
Virtual Address 7fd7
Virtual Address 390e
Virtual Address 748b
```

����𰸿������±�ʾ��
```
Virtual Address 7570:
  --> pde index:0x1d  pde contents:(valid 1, pfn 0x33)
    --> pte index:0xb  pte contents:(valid 0, pfn 0x7f)
      --> Fault (page table entry not valid)
      
Virtual Address 21e1:
  --> pde index:0x8  pde contents:(valid 0, pfn 0x7f)
      --> Fault (page directory entry not valid)

Virtual Address 7268:
  --> pde index:0x1c  pde contents:(valid 1, pfn 0x5e)
    --> pte index:0x13  pte contents:(valid 1, pfn 0x65)
      --> Translates to Physical Address 0xca8 --> Value: 16
```



��3����������ԭ��ζ���ҳ�����⣬���ο�Lab2��ҳ��Ĺ��̣����һ��Ӧ�ó��򣨿ɻ���python, ruby, C, C++��LISP�ȣ���ģ��ʵ��(2)���������ĳ���OS������ȷ��ɶ���ҳ��ת����


��4����������һ̨֧��[����ҳ��](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)�Ļ����������������Ʋ���ϵͳ֧���������ͼ�������������Ʒ�����

 (5)[X86��ҳ��ṹ](http://os.cs.tsinghua.edu.cn/oscourse/OS2015/lecture06#head-1f58ea81c046bd27b196ea2c366d0a2063b304ab)
--- 

## ��չ˼����

�Ķ�64bit IBM Powerpc CPU�ܹ������ʵ��[����ҳ��](http://en.wikipedia.org/wiki/Page_table#Inverted_page_table)�������������档

--- 