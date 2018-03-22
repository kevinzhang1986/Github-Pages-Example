---
layout:     post
title:      基础篇——UEFI里的事件和TPL优先级
category: 技术总结
description: UEFI里的事件和TPL优先级
tags: UEFI
---

# 基础篇——UEFI里的事件和TPL优先级

## 1 EFI_EVENT
由于UEFI内部没有中断（时钟中断除外），所有异步操作都是通过事件来完成的，因此事件就变得尤为重要。
和Handle、iHandle的关系类似，事件也有EFI_EVENT和iEvent。
```
typedef VOID                      *EFI_EVENT;
```
```
typedef struct {
  UINTN                   Signature;
  UINT32                  Type;
  UINT32                  SignalCount;
  ///
  /// Entry if the event is registered to be signalled
  ///
  LIST_ENTRY              SignalLink;
  ///
  /// Notification information for this event
  ///
  EFI_TPL                 NotifyTpl;
  EFI_EVENT_NOTIFY        NotifyFunction;
  VOID                    *NotifyContext;
  EFI_GUID                EventGroup;
  LIST_ENTRY              NotifyLink;
  BOOLEAN                 ExFlag;
  ///
  /// A list of all runtime events
  ///
  EFI_RUNTIME_EVENT_ENTRY RuntimeData;
  TIMER_EVENT_INFO        Timer;
} IEVENT;
```
我们来看一下，UEFI是怎么实现异步操作的。时钟中断回调里会调用RestoreTpl，然后RestoreTpl调用CoreDispatchEventNotifies进行事件的分发。<br>
CoreRestoreTpl->CoreDispatchEventNotifies。

创建时，通过CoreCreateEvent或者CoreCreateEventEx调用CoreCreateEventInternal来创建IEVENT。如果是EVT_RUNTIME或者EVT_NOTIFY_SIGNAL，会把IEVENT插入对应的链表中。其他类型则返回EFI_EVENT指针。<br>
gEventQueue是所有优先级任务的回调链表。dispatch事件的时候会查找这个链表。

```
///
/// gEventQueue - A list of event's to notify for each priority level
///
LIST_ENTRY      gEventQueue[TPL_HIGH_LEVEL + 1];
```

下面三个函数，会调用CoreNotifyEvent把事件放到gEventQueue队列里。<br>
![](images\2018-3-15-event\1.jpg) 

## 2 常用API （略）
生产者：

消费者：

使用举例：

## 3 TPL
TPL表示当前任务的优先级。通过一个gEfiCurrentTpl全局变量控制。
```
typedef UINTN                     EFI_TPL;
```
对应的API在gBS里。
```
  (EFI_RAISE_TPL)                               CoreRaiseTpl,                             // RaiseTPL
  (EFI_RESTORE_TPL)                             CoreRestoreTpl,                           // RestoreTPL
```
## 4 lock 
EFI_LOCK是使用TPL实现的。当需要锁定原子操作时，需要提升TPL，做完相应的操作后恢复TPL。EFI_LOCK为如下结构体：
```
//
// Lock.c
//
typedef struct {
  EFI_TPL Tpl;
  EFI_TPL OwnerTpl;
  UINTN   Lock;
} EFI_LOCK;
```
```
/**
  Raising to the task priority level of the mutual exclusion
  lock, and then acquires ownership of the lock.

  @param  Lock               The lock to acquire

  @return Lock owned

**/
VOID
CoreAcquireLock (
  IN EFI_LOCK  *Lock
  )
{
  ASSERT (Lock != NULL);
  ASSERT (Lock->Lock == EfiLockReleased);

  Lock->OwnerTpl = CoreRaiseTpl (Lock->Tpl);
  Lock->Lock     = EfiLockAcquired;
}



/**
  Releases ownership of the mutual exclusion lock, and
  restores the previous task priority level.

  @param  Lock               The lock to release

  @return Lock unowned

**/
VOID
CoreReleaseLock (
  IN EFI_LOCK  *Lock
  )
{
  EFI_TPL Tpl;

  ASSERT (Lock != NULL);
  ASSERT (Lock->Lock == EfiLockAcquired);

  Tpl = Lock->OwnerTpl;

  Lock->Lock = EfiLockReleased;

  CoreRestoreTpl (Tpl);
}
```