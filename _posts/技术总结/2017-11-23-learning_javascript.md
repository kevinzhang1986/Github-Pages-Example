---
layout:     post
title:      前端三把斧---javascript
category: 技术总结
description: 简单分析javascript语法
tags: front-end
---

## 什么是javascript
JavaScript 是互联网上最流行的脚本语言，这门语言可用于 HTML 和 web，更可广泛用于服务器、PC、笔记本电脑、平板电脑和智能手机等设备。

JavaScript web 开发人员必须学习的 3 门语言中的一门：
- HTML 定义了网页的内容
- CSS 描述了网页的布局
- JavaScript 网页的行为

## 语法简介
>(1) 用法
>HTML 中的脚本必须位于 <script> 与 </script> 标签之间。<br>
>脚本可被放置在 HTML 页面的 <body> 和 <head> 部分中。
>
>**外部的 JavaScript**
>""script" src="myScript.js" "/script"	

>(2) 输出
>- 使用 window.alert() 弹出警告框。
>- 使用 document.write() 方法将内容写到 HTML 文档中。
>- 使用 innerHTML 写入到 HTML 元素。
>- 使用 console.log() 写入到浏览器的控制台。

>(3) 字面量（固定值）
>包括：数字 字符串 表达式 数组 对象 函数 <br>
>JavaScript 对大小写是敏感的。

>(4) 语句
>- JavaScript 语句向浏览器发出的命令。语句的作用是告诉浏览器该做什么。
>- 以分号隔开
>- 忽略多余的空格。 折行用\

>(5) 注释
// /*

>(6) 变量
>var

>(7) 数据类型
>动态类型
>var x;               // x 为 undefined
>var x = 5;           // 现在 x 为数字
>var x = "John";      // 现在 x 为字符串

>(8) 对象
>var car = {type:"Fiat", model:500, color:"white"};

>(9)函数
>传递值

>(10)作用域

>(11)事件

>(12)正则表达式

>(13)变量提升

>(14)表单

>(15)JSON
>- JSON 是用于存储和传输数据的格式。
>- JSON 通常用于服务端向网页传递数据 。
>
>* JSON 英文全称 JavaScript Object Notation
>* JSON 是一种轻量级的数据交换格式。
>* JSON是独立的语言 *
>* JSON 易于理解。
>
>JSON.parse()
>JSON.stringify()

其余参考：
- [菜鸟学堂]http://www.runoob.com/js/js-tutorial.html