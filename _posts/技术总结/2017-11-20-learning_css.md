---
layout:     post
title:      前端三把斧---CSS
category: 技术总结
description: 简单分析css语法
tags: html
---

## 什么是CSS
- CSS 指层叠样式表 (Cascading Style Sheets)
- 样式定义**如何显示 **HTML 元素
- 样式通常存储在**样式表**中
- 把样式添加到 HTML 4.0 中，是为了**解决内容与表现分离的**问题
- **外部样式表**可以极大提高工作效率
- 外部样式表通常存储在 **CSS 文件**中
- 多个样式定义可**层叠**为一

## CSS常用语法
CSS 规则由两个主要的部分构成：选择器，以及一条或多条声明: <br>
![](images/2017-11-21-learning-css/css.jpg)

选择器通常是您需要改变样式的 HTML 元素。<br>
每条声明由一个属性和一个值组成。<br>
属性（property）是您希望设置的样式属性（style attribute）。每个属性有一个值。属性和值被冒号分开。<br>


>(1) id 和 class 选择器
>#para1
>{
>    text-align:center;
>    color:red;
>}
>.center {text-align:center;}

>(2) 创建
>- 外部样式表(External style sheet)
"link rel="stylesheet" type="text/css" href="mystyle.css"
>- 内部样式表(Internal style sheet)
"style" ... "/style"
>- 内联样式(Inline style)
>"p style="color:sienna;margin-left:20px""这是一个段落。"/p"
>优先级：
>内联样式）Inline style > （内部样式）Internal style sheet >（外部样式）External style sheet > 浏览器默认样式

>(3) 背景
>- background-color
>- background-image
>- background-repeat
>- background-attachment
>- background-position
>简写属性：body {background:#ffffff url('img_tree.png') no-repeat right top;}

>(4) 文本
>- color
>- text-align
>- text-decoration
>- text-transform
>- text-indent

>(5) 字体
>font-family
>font-style:normal italic oblique
>font-size: 30px 30em

>(6) 链接
>a:link - 正常，未访问过的链接
>a:visited - 用户已访问过的链接
>a:hover - 当用户鼠标放在链接上时
>a:active - 链接被点击的那一刻

>(7)列表样式
>ul.a {list-style-type: circle;}

>(8)表格
>border
>border-collapse:collapse
>width
>height
>text-align
>padding
>background-color
>color

>(9)盒子模型(Box Model)
>所有HTML元素可以看作盒子，在CSS中，"box model"这一术语是用来设计和布局时使用。<br>
>CSS盒模型本质上是一个盒子，封装周围的HTML元素，它包括：边距，边框，填充，和实际内容。<br>
>盒模型允许我们在其它元素和周围元素边框之间的空间放置元素。<br>
>下面的图片说明了盒子模型(Box Model)：<br>
![](images/2017-11-21-learning-css/css.jpg)
>不同部分的说明：<br>
>- Margin(外边距) - 清除边框外的区域，外边距是透明的。
>- Border(边框) - 围绕在内边距和内容外的边框。
>- Padding(内边距) - 清除内容周围的区域，内边距是透明的。
>- Content(内容) - 盒子的内容，显示文本和图像。<br>
>为了正确设置元素在所有浏览器中的宽度和高度，你需要知道的盒模型是如何工作的。