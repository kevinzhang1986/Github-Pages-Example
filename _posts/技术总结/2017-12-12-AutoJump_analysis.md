---
layout:     post
title:      Python开源项目AutoJump代码分析
category: 技术总结
description: 代码分析
tags: python
---

# Python开源项目AutoJump代码分析

## 一、简介
AutoJump是一个基于Python的开源项目，主要用于简化目录的定位。 原本需要“cd 一长串的路径”到达指定的路径。这个项目会把历史记录放到自定义的数据库中，然后通过索引"j 文件夹"就可以到达指定的路径。这个项目的源码，涵盖了大部分的Python语法知识，值得学习（但是软件架构有点冗余和复杂，不是很好理解）。

## 二、数据结构
**ArgumentParser类**

![](images/2017-12-12-AutoJump_analysis/ArgumentParser.jpg)
![](images/2017-12-12-AutoJump_analysis/ExtendsArgumentParser.jpg)

该类继承于_AttributeHolder和_ActionsContainer类<br>


**_AttributeHolder类**

![](images/2017-12-12-AutoJump_analysis/AttributeHolder.jpg)
![](images/2017-12-12-AutoJump_analysis/ExtendedAttributeHolder.jpg)

**_ActionsContainer类**

![](images/2017-12-12-AutoJump_analysis/ActionsContainer.jpg)
![](images/2017-12-12-AutoJump_analysis/ExtendedActionsContainer.jpg)

## 三、流程