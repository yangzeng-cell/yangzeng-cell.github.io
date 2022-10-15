---
title: 前端八股文-html-css
date: 2022-09-04 10:32:27
tags:
categories:
- [前端八股文]
---

# 01-HTML-CSS-常⻅⾯试题

## **1.HTML** **标签有哪些⾏内元素**

```
a

img

picture

span

input

textarea

select

labe
```

## **2.说说你对元素语义化的理解**

元素语义化就是⽤正确的元素做正确的事情。虽然在理论上，所以的html元素都可以通过css样式实现

相同的事情，但是这么做会使事情复杂化，所以我们需要元素语义化来降低复杂度。

元素语义化在我们实际的开发中有很多好处，⽐如：

提⾼代码的阅读性和可维护性;

减少coder之间的沟通成本;

能让语⾳合成⼯具正确识别⽹⻚元素的⽤途，以便做出正确的反应

有利于SEO(Search Engine Optimization)

## **3.HTML** **中有哪些语义化标签**

- header
- footer
- main
- aside
- article
- section
- address
- summary/details
- menu
- h1/h2/h3/h4/h5/h6
- img
- p
- strong/italic

## **4.什么是** **URL** **编码** **(URL Encode)**

encodeURI ⽤来编码**URI**，其不会编码保留字符。

encodeURIComponent ⽤来编码 URI**参数**，除了字符：A-Z a-z 0-9 - _ . ! ~ * ' ( )，都将会转义

## **5.说说你对**SEO****的理解

SEO就是搜索引擎优化(Search Engine Optimization)，SEO通过了解搜索引擎的运⾏规则来调整⽹站，

以提⾼⽹站的曝光度,以及⽹站的排名。

Google 搜索引擎的⼯作流程主要分为三个阶段：

**抓取**：Google 会使⽤名为“抓取⼯具”的⾃动程序搜索⽹络，以查找新⽹⻚或更新后的⽹⻚。Google 会

将这些⽹⻚的地址（即⽹址）存储在⼀个⼤型列表中，以便⽇后查看。我们会通过许多不同的⽅法查找

⽹⻚，但主要⽅法是跟踪我们已知的⽹⻚中的链接。

**编⼊索引**：Google 会访问它通过抓取得知的⽹⻚，并会尝试分析每个⽹⻚的主题。Google 会分析⽹⻚

中的内容、图⽚和视频⽂件，尝试了解⽹⻚的主题。这些信息存储在 Google 索引中，⽽ Google 索引

是⼀个存储在海量计算机中的巨⼤数据库。

**呈现搜索结果**：当⽤户在 Google 上进⾏搜索时，Google 会尝试确定最优质的搜索结果。“最佳”结果取

决于许多因素，包括⽤户的位置、语⾔、设备（桌⾯设备或⼿机）以及先前⽤过的搜索查询。例如，在

⽤户搜索“⾃⾏⻋维修店”后，Google 向巴黎⽤户显示的答案与向⾹港⽤户显示的答案有所不同。⽀付费

⽤不能提⾼⽹⻚在 Google 搜索结果中的排名，⽹⻚排名是完全依靠算法完成的。

## **6.'+'** **与** **'~'** **选择器有什么不同**

~ 是匹配元素之后的选择器

\+ 是匹配相邻元素选择器

```html
<template> <div>我是div</div> <p>我是p</p> <p>我是p</p> <div>我是div</div> <p>我是p</p> <div> <p>我是div下⾯的p</p> <p>我是div下⾯的p</p>
</div>
 <span>我是span</span>
</template> <style>
div+p {
color: red;
 }
/* 第⼀个p标签变红⾊了 */
div~p{
color:red;
 }
/* div后⾯的p标签都变成红⾊了 */
</style>
```

## **7.说明**text-align****居中的条件

- text-align : 直接翻译过来设置⽂本的⽔平对⻬⽅式 (是继承属性)(是继承属性)
- text-align 并不控制块元素⾃⼰的对⻬，只控制它的⾏内内容的对⻬
- MDN解释: 定义⾏内内容（例如⽂字）如何相对它的块⽗元素对⻬(可以设置图⽚居中)
- W3C官⽅⽂档解释: 设置⾏内(inline-level)元素(没有填满⽗元素)在快级⽗元素的对⻬⽅式

## **8. line-height**为什么可以让⽂字垂直居中？

line-height :两⾏⽂字基线( baseline )之间的间距 基线( baseline`):与⼩写字⺟x最底部对⻬的线

⼀⾏⽂本 等于 line-height

⾏⾼ - ⽂本⾼度 = ⾏距

属性值:

normal :取决于⽤户端。桌⾯浏览器（包括Firefox）使⽤默认值，约为1.2，这取决于元素的

font-family

<数字> :该属性的应⽤值是这个⽆单位数字<数字>乘以该元素的字体⼤⼩ 这是设置line

height的推荐⽅法，不会在继承时产⽣不确定的结果

<⻓度> :指定<⻓度>⽤于计算 line box 的⾼度 以 em 为单位的值可能会产⽣不确定的结果

<百分⽐> :与元素⾃身的字体⼤⼩有关。计算值是给定的百分⽐值乘以元素计算出的字体⼤

⼩。百分⽐值可能会带来不确定的结果

height :元素的整体⾼度 line-height :元素中每⼀⾏⽂字所占据的⾼度

假设div中只有⼀⾏⽂字，如何让这⾏⽂字在div内部垂直居中 让 line-height 等同于 height

## **9.**说说盒⼦模型包含哪些内容？

- 内容

  ​     	通过宽度和⾼度设置

- 内边距

  ​		通过padding设置

- padding: 

  ​        padding-top padding-right padding-bottom padding-left;

- 边框

  ​		通过border设置

  ​		border: border-width border-style border-color

  - 外边距

     通过margin设置

    margin: margin-top margin-right margin-bottom margin-left

## **10.**说说你对****margin****的传递和折叠的理解

- margin的传递⼀般是⽗⼦块元素之间,有margin-top传递,margin-bottom传递.
  - margin-top传递: 当块级元素的顶部线和⽗元素的顶部线重叠，那么这个块级元素的margin-top值会传递给⽗元素
  - margin-bottom传递:当块级元素的底部线和⽗元素的底部线重叠，那么这个块级元素的margin
- bottom值会传递给⽗元素
- 折叠: 指的是 垂直⽅向上相邻的2个margin（margin-top、margin-bottom）有可能会合并为1个margin.
- 它有两个兄弟块级元素之间的上下margin的折叠,也有⽗⼦块元素之间的margin折叠

## **11.CSS** **隐藏⻚⾯中某个元素的⼏种⽅法**

display: none

​	通过 CSS 操控 display，移出⽂档流

opacity: 0

​	透明度为 0，仍在⽂档流中，当作⽤于其上的事件(如点击)仍有效

visibility: hidden

​	透明度为 0，仍在⽂档流中，**但作⽤于其上的事件****(****如点击****)****⽆效**，这也是

visibility:hidden 与 opacity: 0 的区别

​	content-visibility

​	移出⽂档流，但是再次显示时消耗性能低

​	绝对定位于当前⻚⾯的不可⻅位置	

```css
position: absolute;
top: -9000px;
left: -9000px;
```

## **12.box-sizing**有什么作⽤？****content-box****和****border-box的区别

box-sizing⽤来设置盒⼦模型中宽⾼的计算⽅式：

​	content-box: padding、border都布置在width、height外边

​	border-box: padding、border都布置在width、height⾥边

## **13.**为什么会发⽣样式抖动

因为没有指定元素具体⾼度和宽度,⽐如数据还没有加载进来时元素⾼度是 100px(假设这⾥是100px)

数据加载进来后,因为有了数据,然后元素被撑⼤,所有出现了抖动

## **14.**说说浮动常⻅的规则？

- 元素⼀旦浮动后, 脱离标准流

  ​	朝着向左或向右⽅向移动，直到⾃⼰的边界紧贴着包含块（⼀般是⽗元素）或者其他浮动元素的边界为⽌

- 定位元素会层叠在浮动元素上⾯

  ​	如果元素是向左（右）浮动，浮动元素的左（右）边界不能超出包含块的左（右）边界

- 浮动元素之间不能层叠

  ​	如果⼀个元素浮动，另⼀个浮动元素已经在那个位置了，后浮动的元素将紧贴着前⼀个浮动元素（左浮找左浮，右浮找右浮）

  ​	如果⽔平⽅向剩余的空间不够显示浮动元素，浮动元素将向下移动，直到有充⾜的空间为⽌

- 浮动元素不能与⾏内级内容层叠，⾏内级内容将会被浮动元素推出

  ​	⽐如⾏内级元素、inline-block元素、块级元素的⽂字内容

  ​	⾏内级元素、inline-block元素浮动后，其顶部将与所在⾏的顶部对⻬

## **15.**为什么需要清除浮动？清除浮动有⼏种⽅法？

**为什么需要清除浮动：**

1）由于浮动元素脱离了标准流，变成了浮动元素，不再向⽗元素汇报⾼度。所以⽗元素在计算⾼度时

并没有将浮动元素的⾼度计算进来，因此就造成了⾼度塌陷的问题 。解决⾼度塌陷的问题就叫做清除浮动（3分）

2）清除浮动的⽬的：是为了让⽗元素在计算⾼度的时候把浮动⼦元素的⾼度计算进去

**清除浮动有⼏种⽅法：**

给⽗元素设置固定⾼度，扩展性不好，不推荐

在⽗元素的最后增加⼀个空的块级⼦元素，并设置让他clear:both , 但是增加了⽆意义的空标签，

违反了结构与样式分离的原则

给⽗元素添加⼀个伪元素(推荐)

```CSS
.clear_fix::after {
content: "";
display: block;
clear:both;
visibility: hidden; /* 浏览器兼容性 */
height: 0; /* 浏览器兼容性 */
}
.clear_fix {
 *zoom: 1; /* IE6/7兼容性 */
}
```

overflflow:auto触发BFC来清除浮动（前提⾼度为auto）

## **16.**伪类与伪元素有什么区别****?

伪类使⽤单冒号，⽽伪元素使⽤双冒号。如 :hover 是伪类， ::before 是伪元素

伪元素会在⽂档流⽣成⼀个新的元素，并且可以使⽤ content 属性设置内容

## **17.**结构伪类****nth-child(n)****和****nth-of-type(n)****的区别？

:nth-child

是结构伪类选择器，选中⽗元素的第⼏个⼦元素 , 计数时与元素的类型⽆关。

:nth-of-type

是结构伪类选择器和nth-child类似，但是计数时只计算同种类型的元素。
