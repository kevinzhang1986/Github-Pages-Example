---
layout:     post
title:      基础篇——UEFI里的Protocol和对应的操作
category: 技术总结
description: UEFI里的Protocolhe和对应的操作
tags: UEFI
---

# 基础篇——UEFI里的Protocol和对应的操作

## 1 什么是Protocol

一个protocol就是一堆函数指针和数据成员的集合,官方称呼为protocol interface structure;

个人认为Protocol除了为UEFI提供了驱动之间的通信接口，另外也是面向对象在UEFI里的体现。
- 封装 用struct封装接口和数据
- 继承 用的比较少
- 多态 多实例，可以有不同实现

其他和C++的比较可以看一下CSDN上的这篇博客[EFI Protocol VS C++](http://blog.csdn.net/hgf1011/article/details/4342311)

## 2 This指针
```
EFI_STATUS
BlockIoReadWrite (
  IN     VOID                    *This,
  IN     UINT32                  MediaId,
  IN     EFI_LBA                 Lba,
  IN OUT EFI_BLOCK_IO2_TOKEN     *Token,
  IN     UINTN                   BufferSize,
  OUT    VOID                    *Buffer,
  IN     BOOLEAN                 IsBlockIo2,
  IN     BOOLEAN                 IsWrite
  )
```
This指针指向协议的实例。<br>
C++的This指针由编译器自动加入，而Protocol成员函数的This指针需手工添加。

## 3 架构协议
Architecture Protocols也是在DXE阶段运行的过程中逐渐填充的。

以下是所有的Architecture Protocols，这些Protocols是UEFI运行所必须的：
```
//
// DXE Core Global Variables for all of the Architectural Protocols.
// If a protocol is installed mArchProtocols[].Present will be TRUE.
//
// CoreNotifyOnArchProtocolInstallation () fills in mArchProtocols[].Event
// and mArchProtocols[].Registration as it creates events for every array
// entry.
//
EFI_CORE_PROTOCOL_NOTIFY_ENTRY  mArchProtocols[] = {
  { &gEfiSecurityArchProtocolGuid,         (VOID **)&gSecurity,      NULL, NULL, FALSE },
  { &gEfiCpuArchProtocolGuid,              (VOID **)&gCpu,           NULL, NULL, FALSE },
  { &gEfiMetronomeArchProtocolGuid,        (VOID **)&gMetronome,     NULL, NULL, FALSE },
  { &gEfiTimerArchProtocolGuid,            (VOID **)&gTimer,         NULL, NULL, FALSE },
  { &gEfiBdsArchProtocolGuid,              (VOID **)&gBds,           NULL, NULL, FALSE },
  { &gEfiWatchdogTimerArchProtocolGuid,    (VOID **)&gWatchdogTimer, NULL, NULL, FALSE },
  { &gEfiRuntimeArchProtocolGuid,          (VOID **)&gRuntime,       NULL, NULL, FALSE },
  { &gEfiVariableArchProtocolGuid,         (VOID **)NULL,            NULL, NULL, FALSE },
  { &gEfiVariableWriteArchProtocolGuid,    (VOID **)NULL,            NULL, NULL, FALSE },
  { &gEfiCapsuleArchProtocolGuid,          (VOID **)NULL,            NULL, NULL, FALSE },
  { &gEfiMonotonicCounterArchProtocolGuid, (VOID **)NULL,            NULL, NULL, FALSE },
  { &gEfiResetArchProtocolGuid,            (VOID **)NULL,            NULL, NULL, FALSE },
  { &gEfiRealTimeClockArchProtocolGuid,    (VOID **)NULL,            NULL, NULL, FALSE },
  { NULL,                                  (VOID **)NULL,            NULL, NULL, FALSE }
};
```
## 4 相关API
OpenProtocol   打开指定设备的某个protocol<br>
HandleProtocol 打开指定设备的某个protocol(OpenProtocol的简化版)<br>

LocateProtocol 打开指定protocol的第一个实例<br>
LocateHandleBuffer 找出支持某个protocol的所有设备<br>
LocateHandle       和LocateHandleBuffer最大的区别是需要调用者负责管理Buffer数组占用的内存。<br>

ProtocolsPerHandle<br>
OpenProtocolInformation<br>

CloseProtocol

InstallProtocolInterface<br>
ReinstallProtocolInterface<br>
UninstallProtocolInterface<br>

InstallMultipleProtocolInterfaces<br>
UninstallMultipleProtocolInterfaces<br>

RegisterProtocolNotify

[参考链接](http://blog.csdn.net/chris_leeyc/article/details/47088177)
