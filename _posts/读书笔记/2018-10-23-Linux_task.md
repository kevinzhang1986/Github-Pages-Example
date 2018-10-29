---
layout:     post
title:      读书笔记-Linux进程简介
category: 读书笔记
description: Linux-进程简介
tags: Linux
---

# Linux-进程简介

## 1 Linux中的进程概述
Linux中的每个进程由一个task_struct数据结构来描述。在Linux2.4中，Linux为每个新创建的进程动态地分配一个task_struct结构。<br>
和其他操作系统类似，Linux也支持两种进程：普通进程和实时进程。task_struct结构是比较庞大的，将所有域按其功能可做如下划分：
• 进程状态（State）；<br>
• 进程调度信息（Scheduling Information）；<br>
• 各种标识符（Identifiers）；<br>
• 进程通信有关信息（IPC，Inter_Process Communication）；<br>
• 时间和定时器信息（Times and Timers）；<br>
• 进程链接信息（Links）；<br>
• 文件系统信息（File System）；<br>
• 虚拟内存信息（Virtual Memory）；<br>
• 页面管理信息（page）；<br>
• 对称多处理器（SMP）信息；<br>
• 和处理器相关的环境（上下文）信息（Processor Specific Context）； <br>
• 其他信息。 <br>

## 2 task_struct结构在内存中的存放
内核栈
```
 union task_union {
 struct task_struct task;
 unsigned long stack[2408];
};
```
当前进程
```
tatic inline struct task_struct * get_current（void）
{
struct task_struct *current;
__asm__（"andl %%esp,%0; ":"=r" （current） : "0" （~8191UL））;
return current;
}
```
## 3 进程的组织方式
* 哈希表<br>

* 双向循环链表<br>

* 运行队列<br>

* 等待队列<br>

备注：注意链表的实现和使用，和相关宏的使用<br>

## 4 内核线程

## 5 进程的权能
CAP_XXX


## 5 内核同步
* 信号量<br>
* 原子操作<br>
* 自旋锁 （可以看一下UEFI里的同步机制和内核的区别）<br>

## 代码理解
