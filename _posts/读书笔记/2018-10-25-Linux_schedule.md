---
layout:     post
title:      读书笔记-Linux进程调度
category: 读书笔记
description: Linux-进程调度
tags: Linux
---

# Linux-进程调度与切换

## 1 Linux时间系统
jiffies

timer_interrupt（ ） -> do_timer_interrupt() -> do_timer（ ） 

初始化：
```
void __init time_init（void）
{
xtime.tv_sec=get_cmos_time();
xtime.tv_usec=0;
setup_irq（0，＆irq0）;
} 
```
```
static struct irqaction irq0 = { timer_interrupt, SA_INTERRUPT, 0, "timer", NULL, NULL}; 


do_timer_interrupt（ ） ／＊这是一个伪函数 ＊／
{
 SAVE_ALL ／＊保存处理机现场 ＊／
 intr_count += 1; ／＊ 这段操作不允许被中断 ＊／
 timer_interrupt() ／＊ 调用时钟中断程序 ＊／
 intr_count -= 1;
 jmp ret_from_intr /* 中断返回函数 ＊／
} 
```

## 2 Linux 的调度程序—Schedule（ ）
一个好的调度算法应当考虑以下几个方面。<br>
（1）公平：保证每个进程得到合理的 CPU 时间。<br>
（2）高效：使 CPU 保持忙碌状态，即总是有进程在 CPU 上运行。<br>
（3）响应时间：使交互用户的响应时间尽可能短。<br>
（4）周转时间：使批处理用户等待输出的时间尽可能短。<br>
（5）吞吐量：使单位时间内处理的进程数量尽可能多。 <br>

### 调度算法

* 时间片轮转调度算法 <br>

* 优先权调度算法: 非抢占式 抢占式<br>

* 多级反馈队列：综合了时间片轮转调度和抢占式优先权调度的优点，即：优先权高的进程先运行给定的时间片，相同优先权的进程轮流运行给定的时间片。 <br>

* 实时调度<br>

### 调度时机
（1）进程状态转换的时刻：进程终止、进程睡眠；<br>
（2）当前进程的时间片用完时（current->counter=0）；<br>
（3）设备驱动程序；<br>
（4）进程从中断、异常及系统调用返回到用户态时。<br> 

```
cmpl $0, _need_resched
jne reschedule
 ……
restore_all:
 RESTORE_ALL

reschedule:
 call SYMBOL_NAME（schedule）
 jmp ret_from_sys_call 
```

### 调度的依据
need_resched、nice、counter、policy 及 rt_priority 

（1）need_resched: 在调度时机到来时，检测这个域的值，如果为 1，则调用 schedule() 。<br>
（2）counter: 进程处于运行状态时所剩余的时钟滴答数<br>
（3）nice: 进程的“静态优先级”<br>
（4）rt_priority: 实时进程的优先级 <br>
（5）policy: 从整体上区分实时进程和普通进程<br>

### 进程可运行程度的衡量 
函数 goodness()

### 进程调度的实现

```
asmlinkage void schedule（void）
{
 struct task_struct *prev, *next, *p; ／* prev 表示调度之前的进程,
 next 表示调度之后的进程 *／
 struct list_head *tmp;
 int this_cpu, c;
 if （!current->active_mm） BUG();/*如果当前进程的 active_mm 为空，出错*／
need_resched_back:
 prev = current; ／*让 prev 成为当前进程 *／ 
深入分析 Linux 内核源代码
– 138 –
 this_cpu = prev->processor;
if （in_interrupt()） {／*如果 schedule 是在中断服务程序内部执行，
就说明发生了错误*／
 printk（"Scheduling in interrupt\n"）;
 BUG();
 }
 release_kernel_lock（prev, this_cpu）; /*释放全局内核锁，
并开 this_cpu 的中断*／
 spin_lock_irq（&runqueue_lock）; ／*锁住运行队列，并且同时关中断*/
 if （prev->policy == SCHED_RR） ／*将一个时间片用完的 SCHED_RR 实时
 goto move_rr_last; 进程放到队列的末尾 *／
 move_rr_back:
 switch （prev->state） { ／*根据 prev 的状态做相应的处理*／
 case TASK_INTERRUPTIBLE: /*此状态表明该进程可以被信号中断*/
 if （signal_pending（prev）） { /*如果该进程有未处理的
信号，则让其变为可运行状态*/
 prev->state = TASK_RUNNING;
 break;
 }
 default: ／*如果为可中断的等待状态或僵死状态*／
 del_from_runqueue（prev）; ／*从运行队列中删除*／
 case TASK_RUNNING:;／*如果为可运行状态，继续处理*／
 }
 prev->need_resched = 0;

 ／*下面是调度程序的正文 *／
repeat_schedule: ／*真正开始选择值得运行的进程*／
 next = idle_task（this_cpu）; /*缺省选择空闲进程*/
 c = -1000;
 if （prev->state == TASK_RUNNING）
 goto still_running;
still_running_back:
 list_for_each（tmp, &runqueue_head） { ／*遍历运行队列*／
 p = list_entry（tmp, struct task_struct, run_list）;
 if （ can_schedule （ p, this_cpu ） ） { ／ * 单 CPU 中 ， 该 函 数 总 返 回 1* ／
int weight = goodness（p, this_cpu, prev->active_mm）;
 if （weight > c）
 c = weight, next = p;
 }
 }

／* 如果 c 为 0，说明运行队列中所有进程的权值都为 0，也就是分配给各个进程的
 时间片都已用完，需重新计算各个进程的时间片 *／
 if （!c） {
 struct task_struct *p;
 spin_unlock_irq（&runqueue_lock）;／*锁住运行队列*／
 read_lock（&tasklist_lock）; ／* 锁住进程的双向链表*／
 for_each_task（p） ／* 对系统中的每个进程*／
 p->counter = （p->counter >> 1） + NICE_TO_TICKS（p->nice）;
 read_unlock（&tasklist_lock）;
 spin_lock_irq（&runqueue_lock）; 

 goto repeat_schedule;
 }
 spin_unlock_irq（&runqueue_lock）;／*对运行队列解锁，并开中断*／
 if （prev == next） { /*如果选中的进程就是原来的进程*/
 prev->policy &= ~SCHED_YIELD;
 goto same_process;
 }
 ／* 下面开始进行进程切换*／
 kstat.context_swtch++; ／*统计上下文切换的次数*／

 {
 struct mm_struct *mm = next->mm;
 struct mm_struct *oldmm = prev->active_mm;
 if （!mm） { ／*如果是内核线程，则借用 prev 的地址空间*／
 if （next->active_mm） BUG();
 next->active_mm = oldmm;

 } else { ／*如果是一般进程，则切换到 next 的用户空间*／
 if （next->active_mm != mm） BUG();
 switch_mm（oldmm, mm, next, this_cpu）;
 }
 if （!prev->mm） { ／*如果切换出去的是内核线程*／
 prev->active_mm = NULL;／*归还它所借用的地址空间*／
 mmdrop（oldmm）; ／*mm_struct 中的共享计数减 1*／
 }
 }

 switch_to（prev, next, prev）; ／*进程的真正切换，即堆栈的切换*／
 __schedule_tail（prev）; ／*置 prev->policy 的 SCHED_YIELD 为 0 *／
same_process:
 reacquire_kernel_lock（current）;／*针对 SMP*／
 if （current->need_resched） ／*如果调度标志被置位*／
 goto need_resched_back; ／*重新开始调度*／
 return;
} 
```

## 2 进程切换
当进行任务切换时，怎样自动更换堆栈？我们知道，新任务的内核栈指针（SS0 和 ESP0）应当取自当前任务的 TSS，
可是，Linux 中并不是每个任务就有一个 TSS，而是每个 CPU 只有一个 TSS。Intel 原来的意图是让 TR 的内容（即 TSS）随着任务的切换而走马灯似地换，而在 Linux 内核中却成了只更换 TSS 中的 SS0 和 ESP0，而不更换 TSS 本身，也就是根本不更换 TR 的内容。

switch_to 宏
