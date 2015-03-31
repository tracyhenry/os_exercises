#lec9 虚存置换算法spoc练习

## 个人思考题
1. 置换算法的功能？

2. 全局和局部置换算法的不同？

3. 最优算法、先进先出算法和LRU算法的思路？

4. 时钟置换算法的思路？

5. LFU算法的思路？

6. 什么是Belady现象？

7. 几种局部置换算法的相关性：什么地方是相似的？什么地方是不同的？为什么有这种相似或不同？

8. 什么是工作集？

9. 什么是常驻集？

10. 工作集算法的思路？

11. 缺页率算法的思路？

12. 什么是虚拟内存管理的抖动现象？

13. 操作系统负载控制的最佳状态是什么状态？

## 小组思考题目

----
(1)（spoc）请证明为何LRU算法不会出现belady现象

- 设总共访存`n`次，物理页面个数为`k`的LRU算法栈为`S(k)`，那么首先证明如下命题1：
  - `命题1`： 对于任意时刻`t`(1 <= `t` <= `n`)，`S(k)`中的元素都存在于`S(k+1)`中，并且，对于同一个映射的元素，`S(k+1)`的Index不小于`S(k)`的Index。

- 命题1的证明，可以通过对访存的次数`t`进行数学归纳：
  - Base Case: 当`t` = 1时，`S(k)`和`S(k+1)`中都只有访存的第一个元素
  - 当`t` > 1时，对于新进来的元素`V`，有四种情况：
    - `V`属于`S(k)`，`V`属于`S(k+1)`，此时只是把`V`从两个栈都提到了栈顶，命题1中的性质仍然存在；
    - `V`属于`S(k)`，`V`不属于`S(k+1)`，这种情况实际上是不存在的，违反了假设的前提；
    - `V`不属于`S(k)`，`V`属于`S(k+1)`，这种情况之后，`V`均在栈顶，`S(k)`中的元素均比`S(k+1)`的Index小1；
    - `V`不属于`S(k)`，`V`不属于`S(k+1)`，这时，要踢除两个栈的最后一个元素，无论`S(k+1)`的最后一个元素在不在`S(k)`中，命题1的性质仍然存在。
  - 这样，通过数学归纳，命题1得到了证明。

- 根据命题1，无论什么时候，物理页面更多的栈，总是包含了物理页面更少的栈，这样，缺页次数肯定不会更多。所以，LRU算法不会产生Belady现象。

(2)（spoc）根据你的`学号 mod 4`的结果值，确定选择四种替换算法（0：LRU置换算法，1:改进的clock 页置换算法，2：工作集页置换算法，3：缺页率置换算法）中的一种来设计一个应用程序（可基于python, ruby, C, C++，LISP等）模拟实现，并给出测试。请参考如python代码或独自实现。
 - [页置换算法实现的参考实例](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab3/page-replacement-policy.py)
 
## 扩展思考题
（1）了解LIRS页置换算法的设计思路，尝试用高级语言实现其基本思路。此算法是江松博士（导师：张晓东博士）设计完成的，非常不错！

参考信息：

 - [LIRS conf paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang02_LIRS.pdf)
 - [LIRS journal paper](http://www.ece.eng.wayne.edu/~sjiang/pubs/papers/jiang05_LIRS.pdf)
 - [LIRS-replacement ppt1](http://dragonstar.ict.ac.cn/course_09/XD_Zhang/(6)-LIRS-replacement.pdf)
 - [LIRS-replacement ppt2](http://www.ece.eng.wayne.edu/~sjiang/Projects/LIRS/sig02.ppt)
 
