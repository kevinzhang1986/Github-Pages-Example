---
layout:     post
title:      基础篇——UEFI里的Image及加载
category: 技术总结
description: UEFI里的Image以及加载
tags: UEFI
---

# 基础篇——UEFI里的Image及加载

为什么要写UEFI的Image？因为镜像格式非常重要，是安全启动(authenticode_pe格式)的基础，也是理解编译，链接和加载的基础。

## 1 什么是UEFI Image
UEFI里驱动和应用程序会被编译成.efi的二进制，.efi属于UEFI image,[UEFI Images](http://blog.csdn.net/CeliaQianhj/article/details/6763071) 是UEFI定义的、包含可执行代码的一类文件，最显著的特征是包含一个用来定义这段可执行代码格式的PE/COFF header，这个header定义了Processor Type和Image Type。（Microsoft Portable Executable and Common Object File Format Specification (Microsoft 2008)）

```
//
// PE32+ Subsystem type for EFI images
//
#define EFI_IMAGE_SUBSYSTEM_EFI_APPLICATION         10
#define EFI_IMAGE_SUBSYSTEM_EFI_BOOT_SERVICE_DRIVER 11
#define EFI_IMAGE_SUBSYSTEM_EFI_RUNTIME_DRIVER      12
#define EFI_IMAGE_SUBSYSTEM_SAL_RUNTIME_DRIVER      13

//
// BugBug: Need to get a real answer for this problem. This is not in the
//         PE specification.
//
//         A SAL runtime driver does not get fixed up when a transition to
//         virtual mode is made. In all other cases it should be treated
//         like a EFI_IMAGE_SUBSYSTEM_EFI_RUNTIME_DRIVER image
//
#define EFI_IMAGE_SUBSYSTEM_SAL_RUNTIME_DRIVER  13

//
// PE32+ Machine type for EFI images
//
#define IMAGE_FILE_MACHINE_I386     0x014c
#define IMAGE_FILE_MACHINE_IA64     0x0200
#define IMAGE_FILE_MACHINE_EBC      0x0EBC
#define IMAGE_FILE_MACHINE_X64      0x8664
#define IMAGE_FILE_MACHINE_ARM      0x01c0  // Thumb only
#define IMAGE_FILE_MACHINE_ARMT     0x01c2  // 32bit Mixed ARM and Thumb/Thumb 2  Little Endian
#define IMAGE_FILE_MACHINE_ARM64    0xAA64  // 64bit ARM Architecture, Little Endian
```

## 2 UEFI Image的格式
![](images\2018-3-14-Image\1) <br>

EFI文件会包含在FFS里，FFS然后组成FV，最后FV组成FD文件。
FV FFS的概念参考[链接](http://blog.csdn.net/jiangwei0512/article/details/53175131)


## 2 UEFI Image的加载
UEFI通过gBS->LoadImage()加载Image到内存，通过gBS->StartImage()运行Image的代码。

LoadImage调用过程：<br>
CoreLoadImage->CoreLoadImageCommon->CoreLoadPeImage<br>
源码分析（待补充）。

### 扩展阅读
《程序员的自我修养—链接、装载与库》