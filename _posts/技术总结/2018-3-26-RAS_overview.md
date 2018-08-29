---
layout:     post
title:      基础篇(RAS)—什么是RAS
category: 技术总结
description: 础篇(RAS)—什么是RAS
tags: RAS
---

# RAS——什么是RAS

## 概述
我们讲到RAS，宏观上指包括硬件，固件，操作系统，管理软件在内的整套解决方案。微观上，也可以指芯片的RAS特性。当前云，HPC等市场用的芯片不是基于X86就是ARM。但由于芯片厂商的保密协议，我们不能对内部实现一探究竟。因此，本文就从外部初步探索一下RAS。

- Reliablity(可靠性)指的是系统能够正确产生输出结果达到一个给定时限的能力。这意味着一个具有可靠性的系统必须能够检测某些错误并能够做到自修复，或者对于没法修复的错误也能够进行给力并上报更高层次的自恢复机制处理，或者中止问题部件并保障系统其余部分正常运转，更严重地，能够中止整个系统的工作并报告相应的错误。可靠性通常用FIT(Failure in Time)或MTBF（Mean Time Between Failure）来度量。1 FIT = 1 error in 1 Billion hours，约等于11.4万年MTBF。

- Availiability(可用性)指的是保持运行的能力。通常用一定时间段内的宕机事件（例如每年宕机n分钟/小时）或者系统实际运行时间的百分比（如5个9,99,999%）来衡量。
99.9%    8.76 hours/year
99.99%   52 minutes/year
99.999%  5.25 minutes/year
79%的企业要求至少99.99% （2016年的数据）。

- Serviceability（可服务性）指的是系统能够提供便利的诊断功能。

## 哪些市场关注RAS
- DC Infrastructure（企业）
- Cloud
- HPC
- Workstation

## RAS的4大支柱
- 规避：从系统及各模块的设计甚至采购来提供可靠性（例如服务器的电源，风扇都有冗余）；<br>
- 检测：系统各个模块通过检错机制进行检测，比如ECC，Parity, CRC等算法；<br>
- 纠正：包含硬件层面的纠正（比如ECC或者retry机制）和软件层面（FW，OS，Hypervisor和APP）的纠正和恢复；<br>
- 重配：就是故障管理，固件会收集错误信息上报给OS和BMC，并进行重配。
