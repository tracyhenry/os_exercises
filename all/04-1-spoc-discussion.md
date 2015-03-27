#lec8 �����ڴ�spoc��ϰ


NOTICE
- ��"w4l2"��ǵ���������Ҫ�ύ��ѧ�������ϵġ�
- ��"w4l2"��"spoc"��ǵ�����Ҫ�����廪ѧ�ֵ�ͬѧҪ��ʵ�������ɣ�����ʱ�ύ��ѧ����Ӧ��git repo�ϡ�
- ��"hard"��ǵ�����һ���Ѷȣ�����ʵ�֡�
- ��"easy"��ǵ��������ʵ�֣�����ʵ�֡�
- ��"midd"��ǵ�����һ��ˮƽ������ʵ�֡�


## ����˼����

### �ڴ���ʾֲ��Ե�Ӧ�ó�������
---
(1)(w4l2)������һ�������ڴ���ʾֲ��Ժõļ�Ӧ�ó������ӣ���ο�����linux��дһ����Ӧ�ó��������ڴ�ֲ��Բ��������ִ��ʱ�䡣
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
�������µ���������������д˳���
```
gcc -O0 -o goodlocality goodlocality.c
time ./goodlocality
```
���Կ�����ִ��ʱ�䡣

## С��˼����Ŀ
----

### ȱҳ�쳣Ƕ��

��1��ȱҳ�쳣�����������ڴ�����С�������жϷ��������н���ȱҳ�쳣�Ĵ���ʱ���ٴγ���ȱҳ�쳣����ʱ�����ϵͳ�������Ӳ��������δ����������ĺ�����ƺͽ��͡�

### ȱҳ�жϴ�������
��2�����80386������һ������ָ��(ָ�ֳ�4���ֽ�)���书���ǰ�һ��32λ�ֵ�����װ��Ĵ�����ָ��������Ҫװ��������ڵ�32λ��ַ����������������𼸴�ȱҳ�жϣ�
> ��ʾ���ڴ��е�ָ������ݵĵ�ַ��Ҫ���ǵ�ַ����Ͳ����������������Ҫ����ҳĿ¼����invalid��ҳ����invalid��TLBȱʧ���Ƿ������жϣ�

### ����ҳʽ�洢�ĵ�ַת��

��3��(spoc) ��һ̨����ļ������ҳ��С��page size��Ϊ32 Bytes��֧��8KB�������ַ�ռ䣨virtual address space��,��4KB�������ڴ�ռ䣨physical memory�������ö���ҳ��һ��ҳĿ¼�page directory entry ��PDE����СΪ1 Byte,һ��ҳ���page-table entries
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
VALID==1��ʾ����ʾӳ����ڣ�VALID==0��ʾ����ʾ�ڴ�ӳ�䲻���ڣ������������a.��Ӧ������ҳ֡swap out��Ӳ���ϣ�b.��û�����ڴ��У�ҳû����Ӳ���ϣ���ʱҳ֡��Ϊ0x7F����
PFN6..0:ҳ֡�Ż�����еĺ�ҳ��
PT6..0:ҳ��������ַ>>5
```

�Ѿ���������1��ҳĿ¼���8��ҳ����ҳĿ¼���indexΪ0~7��ҳĿ¼��ֱ��Ӧ����8��ҳ��

��[�����ڴ�ģ�������ļ�](./04-1-spoc-memdiskdata.md)�У�������4KB�����ڴ�ռ��4KBdisk�ռ��ֵ��PDBR��ֵ��

��ش��������ַ�Ƿ��кϷ���Ӧ�������ڴ棬�������Ӧ��pde index, pde contents, pte index, pte contents��the value of addr in phy page OR disk sector��
```
Virtual Address 6653:
Virtual Address 1c13:
Virtual Address 6890:
Virtual Address 0af6:
Virtual Address 1e6f:
```

**��ʾ:**
```
ҳ��С��page size��Ϊ32 Bytes(2^5)
ҳ����1B

8KB�������ַ�ռ�(2^13)
һ��ҳ��2^5
PDBR content: 0xd80��1101_100 0_0000, page 0x6c��

page 6c: e1(1110 0001) b5(1011 0101) a1(1010 0001) c1(1100 0001)
         b3(1011 0011) e4(1110 0100) a6(1010 0110) bd(1011 1101)
����ҳ��2^5
ҳ��ƫ�ƣ�2^5

4KB�������ڴ�ռ䣨physical memory��(2^12)
����֡�ţ�2^7

Virtual Address 0330(0 00000 11001 1_0000):
  --> pde index:0x0(00000)  pde contents:(0xe1, 11100001, valid 1, pfn 0x61(page 0x61))
  page 6c: e1 b5 a1 c1 b3 e4 a6 bd 7f 7f 7f 7f 7f 7f 7f 7f
           7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f
  page 61: 7c 7f 7f 4e 4a 7f 3b 5a 2a be 7f 6d 7f 66 7f a7
           69 96 7f c8 3a 7f a5 83 07 e3 7f 37 62 30 7f 3f 
    --> pte index:0x19(11001)  pte contents:(0xe3, 1 110_0011, valid 1, pfn 0x63)
  page 63: 16 00 0d 15 00 1c 1d 16 02 02 0b 00 0a 00 1e 19
           02 1b 06 06 14 1d 03 00 0b 00 12 1a 05 03 0a 1d
      --> To Physical Address 0xc70(110001110000, 0xc70) --> Value: 02

Virtual Address 1e6f(0 001_11 10_011 0_1111):
  --> pde index:0x7(00111)  pde contents:(0xbd, 10111101, valid 1, pfn 0x3d)
  page 6c: e1 b5 a1 c1 b3 e4 a6 bd 7f 7f 7f 7f 7f 7f 7f 7f
           7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f 7f
  page 3d: f6 7f 5d 4d 7f 04 29 7f 1e 7f ef 51 0c 1c 7f 7f
           7f 76 d1 16 7f 17 ab 55 9a 65 ba 7f 7f 0b 7f 7f 
    --> pte index:0x13  pte contents:(0x16, valid 0, pfn 0x16)
  disk 16: 00 0a 15 1a 03 00 09 13 1c 0a 18 03 13 07 17 1c 
           0d 15 0a 1a 0c 12 1e 11 0e 02 1d 10 15 14 07 13
      --> To Disk Sector Address 0x2cf(0001011001111) --> Value: 1c
```

## ��չ˼����
---
(1)�����ԭ��ε�ȱҳ�쳣�Ĵ���������lab3�е�ȱҳ�쳣�Ĵ������̣��������ȵ��������𣩵���֮ͬ����

(2)��X86-32����ҳʽ�洢ϵͳ�У��ٶ���һ��ҳ�����ʼ��ַ��0xE8A3 B000�����̵�ַ�ռ�ֻ�е�һ��ҳ���4KB���ڴ档������4KB�������ַ�Ƕ��٣�����Ӧ�ĵ�һ��ҳ����͵ڶ���ҳ����������ַ�Ƕ��٣�ҳ�����������ʲô��
