---
layout:     post
title:      基础篇——UEFI里的Handle和Handle Database
category: 技术总结
description: UEFI里的Handle和Handle Database
tags: UEFI
---
# 基础篇——UEFI里的Handle和Handle Database

## 一、Handle
```
///
/// A collection of related interfaces.
///
typedef VOID                      *EFI_HANDLE;
```

EFI_HANDLE在EDK中是一个指向VOID的指针。UEFI可以用它来表示某个对象，当一个.efi加载到内存，UEFI会为这个文件建立Image Handle；UEFI扫描总线时，会为实际的控制器建立Controller Handles，也有其他的Handle类型。<br>
![](images\2018-3-12-handle\1.jpg) <br>

在UEFI里，EFI_HANDLE可以本质上是IHANDLE;
```
///
/// IHANDLE - contains a list of protocol handles
///
typedef struct {
  UINTN               Signature;
  /// All handles list of IHANDLE
  LIST_ENTRY          AllHandles;
  /// List of PROTOCOL_INTERFACE's for this handle
  LIST_ENTRY          Protocols;      
  UINTN               LocateRequest;
  /// The Handle Database Key value when this handle was last created or modified
  UINT64              Key;
} IHANDLE;
```
AllHandles用于连接所以的Handle，成员Protocols存放属于自己的协议Interface。可以参考《UEFI原理与编程》的图4-1进行理解。

**那么这些 Handle是怎么创建的呢？**
以Imagehandle和device handle为例，结合源码讲解创建过程。<br>
（1）ImageHandle；

函数调用关系：DxeMain->CoreDispatcher->CoreStartImage这里把ImageHandle作为参数传入；
而ImageHandle是在CoreLoadImage->CoreLoadImageCommon赋值；代码片段如下：
```
  //
  // Install HII Package List Protocol onto the image handle
  //
  if (Image->ImageContext.HiiResourceData != 0) {
    Status = CoreInstallProtocolInterface (
               &Image->Handle,
               &gEfiHiiPackageListProtocolGuid,
               EFI_NATIVE_INTERFACE,
               (VOID *) (UINTN) Image->ImageContext.HiiResourceData
               );
    if (EFI_ERROR (Status)) {
      goto Done;
    }
  }
```
接着追CoreInstallProtocolInterface函数，可以发现EFI_HANDLE本质上是IHANDLE，和前面描述一致。
```
  //
  // Success.  Return the image handle
  //
  *ImageHandle = Image->Handle;

  //
  // If caller didn't supply a handle, allocate a new one
  //
  Handle = (IHANDLE *)*UserHandle;
  if (Handle == NULL) {
    Handle = AllocateZeroPool (sizeof(IHANDLE));
    if (Handle == NULL) {
      Status = EFI_OUT_OF_RESOURCES;
      goto Done;
    }
```

（2）ControllerHandle:
举一个ATA BUS的例子，在start()的时候，创建Child handle，RegisterAtaDevice函数：
```
  Status = gBS->InstallMultipleProtocolInterfaces (
                  &AtaDevice->Handle,
                  &gEfiDevicePathProtocolGuid,
                  AtaDevice->DevicePath,
                  &gEfiBlockIoProtocolGuid,
                  &AtaDevice->BlockIo,
                  &gEfiBlockIo2ProtocolGuid,
                  &AtaDevice->BlockIo2,
                  &gEfiDiskInfoProtocolGuid,
                  &AtaDevice->DiskInfo,
                  NULL
                  );
```
类似地，PCI总线驱动里可以看一下RegisterPciDevice函数，也有创建Child Handle的代码。

## 2 Handle Database
Handle Database是通过Protocol相关接口构建和管理。后续单独写Protocol的文章再展开。

Handle数据库的结构如下：<br>
![](images\2018-3-12-handle\2.jpg) <br>