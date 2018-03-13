---
layout:     post
title:      基础篇——UEFI里的ConnectController
category: 技术总结
description: UEFI里的ConnectController
tags: UEFI
---

# 基础篇——UEFI里的ConnectController

## 1 ConnectController简介
gBS->ConnectController用于连接一个或者多个驱动到一个Controller。

当设备驱动Connect的时候，设备handler上会装上IO协议。<br>
![](images\2018-3-13-ConnectContoller\1.jpg) <br>

当总线驱动Connect的时候，还会创建Child Handle。<br>
![](images\2018-3-13-ConnectContoller\2.jpg) <br>

## 2 源码解读
*原型：*
```
typedef
EFI_STATUS 
(EFIAPI *EFI_CONNECT_CONTROLLER) (
  IN  EFI_HANDLE                ControllerHandle,
  IN  EFI_HANDLE                *DriverImageHandle    OPTIONAL,
  IN  EFI_DEVICE_PATH_PROTOCOL  *RemainingDevicePath  OPTIONAL,
  IN  BOOLEAN                   Recursive
  );
```

|入参|描述|
|-|-|
|ControllerHandle|The handle of the controller to which driver(s) are to be connected. |
|DriverImageHandle|A pointer to an ordered list handles that support the EFI_DRIVER_BINDING_PROTOCOL. |
|RemainingDevicePath|hA pointer to the device path that specifies a child of the controller specified by ControllerHandle.  |
|Recursive|If TRUE, then ConnectController() is called recursively until the entire tree of controllers below the controller specified by ControllerHandle have been created.  If FALSE, then the tree of controllers is only expanded one level.|

- 源码路径 DriverSupport.c -> CoreConnectController
- 调用实例有2种(待补充)