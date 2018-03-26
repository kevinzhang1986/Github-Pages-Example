---
layout:     post
title:      基础篇——UEFI里的StatusCode
category: 技术总结
description: UEFI里的StatusCode
tags: UEFI
---

# 1 StatusCode
EFI中StatusCode是提供状态信息用的。在2004年时，Intel有发布《efi-status-codes-specification-v092》标准，用来描述EFI StatusCode的实现架构。后来归到了PI规范中，以PI1.6为例，StatusCode的描述在vol3第六章。<br>
有3种Status Code:<br>
• Progress codes<br>
• Error codes<br>
• Debug codes  <br>
架构：<br>
![](images\2018-3-16-statuscode\1.jpg) 
<br>实际上，DEBUG宏在某些库实现下，也是调用StatusCode相关接口。

# 2 ERROR:CXXXXX:VXXXXXX I0 XX报错
这个告警是Error codes类型的Status code。C表示code，后面的数值表示类型+错误级别构成。
```
//
// Definition of code types, all other values masked by
// EFI_STATUS_CODE_TYPE_MASK are reserved for use by
// this specification.
//
#define EFI_PROGRESS_CODE 0x00000001
#define EFI_ERROR_CODE    0x00000002
#define EFI_DEBUG_CODE    0x00000003

//
// Definitions of severities, all other values masked by
// EFI_STATUS_CODE_SEVERITY_MASK are reserved for use by
// this specification.
//
#define EFI_ERROR_MINOR       0x40000000
#define EFI_ERROR_MAJOR       0x80000000
#define EFI_ERROR_UNRECOVERED 0x90000000
#define EFI_ERROR_UNCONTAINED 0xA0000000
```

v表示value，表示具体的错误原因，32位数据为（class(31-24 bit)、SubClass(23-16 bit)、Operation(15-0 bit)）.

```
#define EFI_STATUS_CODE_CLASS_MASK      0xFF000000
#define EFI_STATUS_CODE_SUBCLASS_MASK   0x00FF0000
#define EFI_STATUS_CODE_OPERATION_MASK  0x0000FFFF
```

I 数值0表示实例；

XX：表示GUID。