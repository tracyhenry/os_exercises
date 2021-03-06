# CPU调度

##单选题

--- 

若当前进程因时间片用完而让出处理机时，该进程应转变为（）状态。
- [x]	就绪
- [ ] 等待
- [ ] 运行
- [ ] 完成

> 解释：只有处于就绪队列中的进程才能得到时间片，因此因为时间片用完而让出CPU的进程应该再次返回到就绪队列中。时间片是轮循调度算法中的概念，所有的进程都会按照顺序被分配一个时间片，当时间片用完时如果进程执没有结束，那么应该让出CPU进入就绪队列等待下一个属于自己的时间片。

最高响应比优先算法的特点是（）
- [ ] 有利于短作业但不利于长作业
- [x] 有利于短作业又兼顾到长作业
- [ ] 不利于短作业也不利于长作业
- [ ] 不利于短作业但有利于长作业

> 解释：最高响应比优先算法的响应值公式为Ｒ＝（ｗ+s)/s，其中w为等待时间，s为服务时间，因此在等待时间相同的情况下优先选择服务时间短的进程，而当服务时间长的进程等待到一定时间后，其响应值会增加到能够被首先选择，避免了一直被服务时间短的进程超过，所以该算法有利于短作业又兼顾到长作业。

在单处理器的多进程系统中，进程什么时候占用处理器和能占用多长时间，取决于（）
- [ ] 进程相应的程序段的长度
- [ ] 进程总共需要运行时间多少
- [x] 进程自身和进程调度策略
- [ ] 进程完成什么功能

>	解释：在单处理器的多进程系统中，系统是依靠所使用的调度策略来对进程进行调度的，而其所采用的调度策略可能不止一种，所以什么时候选择什么进程占用处理器和能占用多长时间并不仅仅取决于进程的某一项特性。

时间片轮转调度算法是为了（）
- [x] 多个终端都能得到系统的及时响应
- [ ] 先来先服务
- [ ] 优先级高的进程先使用CPU
- [ ] 紧急事件优先处理

>	解释：时间片轮转调度算法在选择进程时是按照到达时间进行选择的，所以不存在优先级高的进程，而每个进程每次只能占用同等的CPU时间，所以优先执行的进程并不一定比后执行的进程先完成，对于新加入的进程，只要是队列中等待的进程不是很多，都可以很及时地得到时间片来使用CPU，所以该算法能够使多个终端得到系统的及时响应。

在基于优先级的可抢占的调度机制中，当系统强制使高优先级任务等待低优先级任务时，会发生（）
- [x] 优先级反转
- [ ] 优先级重置
- [ ] 系统错误
- [ ] 死循环

>	解释：优先级反转的定义：（1）可以发生在任何基于优先级的可抢占的调度机制中；（2）当系统内的环境强制使高优先级等待低优先级任务时发生。

下面关于硬时限（hard deadlines）和软时限（soft deadlines）的描述错误的是（）。
- [ ] 如果错过了硬时限，将会发生严重的后果
- [x] 硬时限是通过硬件实现的，软时限是通过软件实现的
- [ ] 如果软时限没有被满足，系统也可以继续运行
- [ ] 硬时限可以保证系统的确定性

>	解释：硬时限是指必须满足的时间限制，如果没有满足可能会导致非常严重的后果；软时限是指在理想情况下应该被满足的时间限制，如果没有被满足，系统可以降低对该时限的要求，以保证不会产生太严重的后果。

下面的调度算法中那个是公平的（）
- [ ] FCFS 先来先服务
- [ ] SPN 短进程优先
- [x] RR 轮循
- [ ] SRT

>	解释：FCFS算法可能导致某些进程长时间占用CPU，所以并不公平；SPN算法可能会使长进程在很长时间内得不到响应，所以也不公平；RR算法由于每个进程都能及时得到响应，并且不会长时间占用CPU，所以是公平的；SRT也就是SPN。

FCFS调度算法的特点不包括（）
- [ ] 简单
- [ ] 平均等待时间变化大
- [x] 不会导致I/O和CPU之间的重叠处理
- [ ] 花费时间少的任务可能排在花费时间长的任务后面

>	解释：FCFS算法的优点是简单，缺点有（1）平均等待时间变化较大；（2）花费时间较少的任务可能排在花费时间较长的任务后面；（3）可能导致i/o和CPU之间的重叠处理。

CPU调度策略的目标不包括（）
- [ ] 减少响应时间
- [x] 提高系统处理单任务的速度
- [ ] 减少等待时间
- [ ] 增加吞吐量

>	解释：系统处理单任务的速度不能通过CPU调度策略来改善，只能通过改善硬件性能和改良系统架构来提高。

有5个批处理作业(A,B,C,D,E)几乎同时到达一个计算中心,估计运行时间分别为2,4,6,8,10分钟,在使用时间片轮转作法（时间片为2分钟）,作业的平均周转时间为（）
- [x] 18分钟
- [ ] 6分钟
- [ ] 14分钟
- [ ] 22分钟

>	解释：进程A在第一次时间片轮转后就完成了，所以等待时间为0；进程B在第二次时间片轮转后完成，等待时间为2+2*3=8；进程C在第三次时间片轮转后完成，等待时间为2+2+2*2+2+2*2=14；进程D在第四次时间片轮转后完成，等待时间为2+2+2+2+2+2+2+2=18；进程E在第五次时间片轮转后完成，等待时间为2+2+2+2+2+2+2+2+2+2+2=20；因此总的周转时间为2+0+4+8+6+14+8+18+10+20=90，所以平均周转时间为90/5=18。

---

##多选题

---

对上下文切换的描述正确的是（）
- [x] 切换CPU的当前任务到另一个任务
- [ ] 不需要保存当前进程在PCB/TCP中的执行上下文
- [x] 需要读取下一个进程的上下文
- [ ] 只能读取没有被执行过的进程

>	解释：上下文切换的相关概念：（1）切换CPU的当前任务，从一个进程到另一个进程；（2）保存当前进程在PCB/TCP的执行上下文；（3）读取下一个进程的上下文。被切换的进程可以是新来的，也可以是之前没有执行完的。

可以作为进程调度算法的有（）。
- [x] 先来先服务调度算法
- [x] 时间片轮转调度算法
- [ ] 最高优先级调度算法
- [x] 最高响应比优先调度算法
- [ ] 均衡调度算法

>	解释：不存在最高优先级调度算法和均衡调度算法。

下面可以作为比较调度算法的指标有（）
- [x] CPU使用率
- [x] 吞吐量
- [x] 周转时间
- [x] 等待时间
- [x] 响应时间

>	解释：衡量调度算法的5个方面：CPU使用率，吞吐量，周转时间，等待时间和响应时间。

CPU调度策略可以通过哪几种方式增加系统的吞吐量（）
- [x] 减少操作系统开销
- [x] 减少上下文切换次数
- [ ] 加快系统处理单个任务的速度
- [x] 高效利用系统资源

>	解释：增加吞吐量可以从两个方面入手：（1）减少开销（操作系统开销，上下文切换）；（2）系统资源的高效利用（CPU，I/O设备）。

下面对FFS公平共享调度控制用户对系统资源的访问的描述中，正确的是（）
- [ ] 所有的用户组都是平等的
- [x] 能够保证不重要的用户组无法垄断资源
- [x] 未使用的资源按照每个组所分配的资源的比例来分配
- [x] 没有达到资源使用率目标的组可以获得更高的优先级

>	解释：公平共享调度控制用户对系统资源的访问：（1）一些用户组比其他用户组重要；（2）保证不重要的组无法垄断资源；（3）未使用的资源按照每个组所分配的资源的比例来分配；（3）没有达到资源使用率目标的组获得更高的优先级。

---

##判断题

---

作业调度选择一个作业装入主存后，该作业能否占用处理器必须由作业控制来决定。
- [ ] 对
- [x] 错

>	解释：作业能够占用处理器是由进程调度来决定的。

在进行作业调度时，要想兼顾作业等待时间和计算时间，可选取响应比高者优先算法。
- [x] 对
- [ ] 错

>	解释：最高响应比优先算法的公式为Ｒ＝（ｗ+s)/s，其中w为等待时间，s为计算时间，所以兼顾作业的等待时间和计算时间。

在作业调度时, 采用最高响应比优先的作业调度算法可以得到最短的作业平均周转时间。
- [ ] 对
- [x] 错

>	解释：短进程优先算法的平均等待时间最小。

轮循算法的时间量子越大越好。（错）
- [ ] 对
- [x] 错

>	解释：轮循算法的时间量子太大的话会导致进程等待的时间过长，极限情况下会退化成FCFS。

可抢占式的调度算法比不可抢占式的调度算法开销要小。（错）
- [ ] 对
- [x] 错

>	解释：可抢占式的调度算法比不可抢占式的调度算法开销要大，因为其上下文切换比不可抢占式的要多。

