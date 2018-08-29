---
layout:     post
title:      基础篇(RAS)—APEI解读
category: 技术总结
description: 基础篇(RAS)—APEI解读
tags: RAS
---

# APEI
ACPI Platform Error Interfaces提供了一种把错误信息提给OSPM的方式。APEI扩展了已有的错误上报机制，并进行了统一。

APEI由4张独立的表组成：<br>
• Error Record Serialization Table (ERST)<br>
• Boot Error Record Table (BERT)<br>
• Hardware Error Source Table (HEST)<br>
• Error Injection Table (EINJ)

## BERT表
用来报告上一次启动发生的，没有处理的错误。
OSPM在启动的时候会去查询启动阶段的错误。平台通过Common Platform Error Record（CPER）compliant error record报告给OSPM。CPER在UEFI规范里定义。
![](images\2018-4-2-acpi-apei\1.jpg) <br>


## HEST表
 Hardware Error Source Table提供了一种平台描述系统硬件错误源给OSPM的方式。

![](images\2018-4-2-acpi-apei\2.jpg) <br>

有下面这些错误源：
- IA-32 Architecture Machine Check Exception
- IA-32 Architecture Corrected Machine Check
- PCI Express Root Port AER Structure
- PCI Express Device AER Structure
- PCI Express/PCI-X Bridge AER Structure
- Generic Hardware Error Source

**参考资料**<br>
[WHEA原理和架构](https://zhuanlan.zhihu.com/p/29344788)