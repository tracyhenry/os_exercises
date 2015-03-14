
#lec 3 SPOC Discussion

## 第三讲 启动、中断、异常和系统调用-思考题

## 3.1 BIOS
 1. 比较UEFI和BIOS的区别。
 1. 描述PXE的大致启动流程。

## 3.2 系统启动流程
 1. 了解NTLDR的启动流程。
 1. 了解GRUB的启动流程。
 1. 比较NTLDR和GRUB的功能有差异。
 1. 了解u-boot的功能。

## 3.3 中断、异常和系统调用比较
 1. 举例说明Linux中有哪些中断，哪些异常？
 1. Linux的系统调用有哪些？大致的功能分类有哪些？  (w2l1)
 
  - LINUX有数百个系统调用，包括文件操作、进程管理、内存管理等类别。
  - 每个类别的具体例子包括：
  - 进程控制：fork
  - 文件操作：open
  - 系统控制：time
 
 1. 以ucore lab8的answer为例，uCore的系统调用有哪些？大致的功能分类有哪些？(w2l1)
 
  - ucore有20多个系统调用，分类为文件操作、进程管理和系统控制。
  - 主要的例子：
  - 进程控制：sys_fork, sys_exec
  - 文件操作：sys_pgdir, sys_read, sys_close
  - 系统控制：sys_gettime
  
## 3.4 linux系统调用分析
 1. 通过分析[lab1_ex0](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex0.md)了解Linux应用的系统调用编写和含义。(w2l1)
  - lab1-ex0.S分别将系统调用号SYS_WRITE、标准输出函数STDOUT以及需要输出的字符串helloworld作为参数压栈，再调用int 0x80调用系统调用。
  - objdump用于反汇编
  - nm显示函数的入口地址
  - file用于显示文件类型。

 2. 通过调试[lab1_ex1](https://github.com/chyyuu/ucore_lab/blob/master/related_info/lab1/lab1-ex1.md)了解Linux应用的系统调用执行过程。(w2l1)
  - strace用来显示系统调用占用的时间、所占时间百分比、调用次数以及出错次数等。
  - 系统调用的一般流程为：
    - 程序员调用库函数
    - 库函数将系统调用所需参数压栈，调用系统调用接口
    - 操作系统根据相应系统调用编号，找到系统调用的入口
    - 操作系统调用相应的硬件来完成系统调用

## 3.5 ucore系统调用分析
 1. ucore的系统调用中参数传递代码分析。
 1. ucore的系统调用中返回结果的传递代码分析。
 1. 以ucore lab8的answer为例，分析ucore 应用的系统调用编写和含义。
 1. 以ucore lab8的answer为例，尝试修改并运行ucore OS kernel代码，使其具有类似Linux应用工具`strace`的功能，即能够显示出应用程序发出的系统调用，从而可以分析ucore应用的系统调用执行过程。
 
## 3.6 请分析函数调用和系统调用的区别
 1. 请从代码编写和执行过程来说明。
   1. 说明`int`、`iret`、`call`和`ret`的指令准确功能
 
