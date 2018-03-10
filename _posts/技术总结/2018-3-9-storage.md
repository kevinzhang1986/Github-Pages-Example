---
layout:     post
title:      基础篇——存储知识梳理
category: 技术总结
description: 存储基础知识整理
tags: storage
---
# 基础篇——存储知识梳理

## 一、逻辑接口

### 1 NVMe

[NVM Express (NVMe)](https://en.wikipedia.org/wiki/NVM_Express)，或成非易失性内存主机控制器接口规范（Non-Volatile Memory Host Controller Interface Specification），是一个逻辑设备接口规范。它是与AHCI类似的，基于设备逻辑接口的总线传输协议规范（相当于通讯协议里的应用层），用于连接基于PCIe总想附加的非易失内存介质（例如采用闪存的固态硬盘驱动器），虽然理论上不一定要求PCIe总线协议。

* 低延迟、内部并发
* 比此前机械硬盘驱动器（HDD）时代的AHCI，NVMe/NVMHCI降低了I/O操作等待事件，提升同一时间内的操作数，更大容量的操作队列等。
* 在NVMe出现之前，高端SSD只得以采用PCI Express总线制造，但需使用非标准接口的规范。
* 2009年Intel开始着手寻找SATA的替代方案，SATA作为串行接口，采用AHCI规范，其已经成为制约SSD速度的瓶颈。AHCI只有1个命令队列，队列深度32。而NVMe可以用65535个命令队列，每个队列都可以深达65536个命令。

### 2 AHCI
[AHCI](https://baike.baidu.com/item/AHCI/3639535?fr=aladdin)（Serial ATA Advanced Host Controller Interface）串行ATA高级主控接口/高级主机控制器接口），是在Intel的指导下，由多家公司联合研发的接口标准，它允许存储驱动程序启用高级串行 ATA 功能，如本机命令队列和热插拔。

*参考资料：*<br>
[AHCI就要过时了？新接口标准NVMe浅析](http://diy.pconline.com.cn/611/6111798.html)
- NVMe精简了调用方式，执行命令时不需要读取寄存器；而AHCI每条命令则需要读取4次寄存器，一共会消耗8000次CPU循环，从而造成2.5μs的延迟。
- 驱动适用性广
- 低功耗<br>

[别了SATA！SSD接口转战PCIe和NVMe](http://finance.people.com.cn/n/2014/0711/c348883-25267559.html)

## 二、 物理接口

### 1 SATA SAS
[SATA（Serial Advanced Technology Attachment，串行高级技术附件](https://baike.baidu.com/item/SATA/286664?fr=aladdin)是一种基于行业标准的串行硬件驱动器接口，是由Intel、IBM、Dell、APT、Maxtor和Seagate公司共同提出的硬盘接口规范。

[SAS（Serial Attached SCSI](https://baike.baidu.com/item/SAS/2632304#viewPageContent)，串行连接SCSI接口，串行连接小型计算机系统接口。

[老司机带你认识服务器硬盘--SAS和SATA盘](http://blog.csdn.net/bearcatfly/article/details/72855489)

## 三、 介质
### 1 SSD

### 2 EMMC

等等。

## 四、 组网
### 1 RAID

*参考资料：*<br> 
[图示介绍DAS、NAS和SAN特点和区别是什么？加上iSCIS?](https://www.zhihu.com/question/24335605?sort=created)

[DAS、NAS、SAN、iSCSI 存储方案概述](http://blog.51cto.com/andygao/822147)

[关于LUN和存储卷的区别详解](http://www.itsto.com/news/174.htmlhttp://www.itsto.com/news/174.html)