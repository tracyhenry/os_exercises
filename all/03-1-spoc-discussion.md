# lec5 SPOC思考题


NOTICE
- 有"w3l1"标记的题是助教要提交到学堂在线上的。
- 有"w3l1"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定难度，鼓励实现。
- 有"easy"标记的题很容易实现，鼓励实现。
- 有"midd"标记的题是一般水平，鼓励实现。


## 个人思考题
---

请简要分析最优匹配，最差匹配，最先匹配，buddy systemm分配算法的优势和劣势，并尝试提出一种更有效的连续内存分配算法 (w3l1)

- 最先匹配：
  - `优点`：
    - 简单，找到第一个合适的就可以
    - 在高地址会有大量的空闲大块
  - `缺点`：
    - 产生外碎片
    - 分配大块慢
- 最优匹配：
  - `优点`：
    - 块的平均尺寸小，效果好
    - 外碎片尺寸小
    - 实现简单
  - `缺点`：
    - 产生很多无用的小外碎片
    - 释放分区比较复杂，因为需要找地址邻近的合并，而排序通常是按照块大小排的
- 最差匹配：
  - `优点`：
    - 简单，找到最大的就行
    - 中等大小分配效果好
    - 小碎片不多
  - `缺点`：
    - 释放分区复杂
    - 容易破坏大分区，对后续大分区的分配比较困难
- Buddy System：
  - `优点`：
    - 释放内存的时候相对容易实现
    - 实现简单
  - `缺点`
    - 容易产生比较大的内碎片

>  

## 小组思考题

请参考ucore lab2代码，采用`struct pmm_manager` 根据你的`学号 mod 4`的结果值，选择四种（0:最优匹配，1:最差匹配，2:最先匹配，3:buddy systemm）分配算法中的一种或多种，在应用程序层面(可以 用python,ruby,C++，C，LISP等高语言)来实现，给出你的设思路，并给出测试用例。 (spoc)

```

#include <cstdio>
#include <vector>
#include <algorithm>
using namespace std;

struct block
{
  int start, size;
  block *pre, *suc;
};

const int MAX = 4000;
block *head, *tail;

void initBlock()
{
  head = new block();
  tail = new block();
  st = new block();
  st->start = 0;
  st->size = MAX;
  st->pre = head;
  st->suc = tail;
  head->pre = null;
  head->suc = st;
  tail->pre = st;
  tail->suc = null;
}

void insertBlock(block* it, int start, int size)
{
  block *curBlock = new block();
  curBlock->size = size;
  curBlock->start = start;
  curBlock->pre = it;
  curBlock->suc = it->suc;
  curBlock->pre->suc = curBlock;
  curBlock->suc->pre = curBlock;
}

void deleteBlock(block *it)
{
  it->pre->suc = it->suc;
  it->suc->pre = it->pre;
  delete it;
}

int insert(int size)
{
  //最优匹配，size从小到大排序
  int leftSize = -1;
  int leftStart = -1;
  for (block *it = head->suc; it != tail; it = it->suc)
    if (it->size >= size)
    {
      leftSize = it->size - size;
      leftStart = it->start;
      deleteBlock(it);
      break;
    }
  
  //没有找到
  if (leftSize == -1)
    return -1;
  
  if (leftSize == 0)
    return leftStart + leftSize;
    
  //插入
  bool inserted = false;
  for (block *it = head->suc; it != tail; it = it->suc)
    if (it->size > leftSize)
    {
      insertBlock(it -> pre, leftStart, leftSize)
      inserted = true;
      break;
    }
  if (! inserted)
    insertBlock(tail->pre, leftStart, leftSize);
  return leftStart + leftSize;
}

void free(int start, int size)
{
  int lo = start, hi = start + size;
  
  //找出free当前块之后影响的所有空闲区，是一个连续的区间
  while (1)
  {
    bool expanded;
    for (block *it = head->suc; it != tail; it = it->suc)
      if (it->start == hi || it->start + it->size == lo)
      {
        lo = min(lo, it->start);
        hi = max(hi, it->start + it->size);
        deleteBlock(it);
        expanded = true;
        break;
      }
  }
  
  //insert this final block
  bool inserted = false;
  for (block *it = head ->suc; it!= tail; it = it->suc)
    if (it->size >= hi - lo)
    {
      insertBlock(it->pre, lo, hi - lo);
      inserted = true;
      break;
    }
  if (! inserted)
    insertBlock(tail->pre, lo, hi - lo);
}

int main()
{
  initBlock();
  int s1, s2, s3;
  
  s1 = insert(1000);  //s1 = 3000
  s2 = insert(1000);  //s2 = 2000
  free(s1, 1000);     //two free blocks now: [0, 2000] & [3000, 4000]
  s3 = insert(1000);  //s3 = 3000
}
```


--- 

## 扩展思考题

阅读[slab分配算法](http://en.wikipedia.org/wiki/Slab_allocation)，尝试在应用程序中实现slab分配算法，给出设计方案和测试用例。

## “连续内存分配”与视频相关的课堂练习

### 5.1 计算机体系结构和内存层次
MMU的工作机理？

- [x]  

>  http://en.wikipedia.org/wiki/Memory_management_unit

L1和L2高速缓存有什么区别？

- [x]  

>  http://superuser.com/questions/196143/where-exactly-l1-l2-and-l3-caches-located-in-computer
>  Where exactly L1, L2 and L3 Caches located in computer?

>  http://en.wikipedia.org/wiki/CPU_cache
>  CPU cache

### 5.2 地址空间和地址生成
编译、链接和加载的过程了解？

- [x]  

>  

动态链接如何使用？

- [x]  

>  


### 5.3 连续内存分配
什么是内碎片、外碎片？

- [x]  

>  

为什么最先匹配会越用越慢？

- [x]  

>  

为什么最差匹配会的外碎片少？

- [x]  

>  

在几种算法中分区释放后的合并处理如何做？

- [x]  

>  

### 5.4 碎片整理
一个处于等待状态的进程被对换到外存（对换等待状态）后，等待事件出现了。操作系统需要如何响应？

- [x]  

>  

### 5.5 伙伴系统
伙伴系统的空闲块如何组织？

- [x]  

>  

伙伴系统的内存分配流程？

- [x]  

>  

伙伴系统的内存回收流程？

- [x]  

>  

struct list_entry是如何把数据元素组织成链表的？

- [x]  

>  


