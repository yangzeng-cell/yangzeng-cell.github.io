---
title: Grid布局
date: 2022-10-19 22:46:44
tags:
- [Grid]
categories:
- [CSS,Grid]
---

# 网格布局的基本概念

Grid是二维的网格布局系统，可用于布局页面主要的区域布局或小型组件，flex是一维的布局

## [什么是网格？](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#什么是网格？)

网格是一组相交的水平线和垂直线，它定义了网格的列和行。我们可以将网格元素放置在与这些行和列相关的位置上。CSS 网格布局具有以下特点

### [固定的位置和弹性的轨道的大小](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#固定的位置和弹性的轨道的大小)

你可以使用固定的单位尺寸来构建网格，例如px,或者使用专门为grid创建的新的单位fr用来创建弹性网格。

### [元素位置](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#元素位置)

你可以用使用行好，行名或者一个标定来精确的定位每个网格的排列

### [创建额外的轨道来包含元素](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#创建额外的轨道来包含元素)

可以使用网格布局定义一个显式网格，但是根据规范它会处理你加在网格外面的内容，当必要时它会自动增加行和列，它会尽可能多的容纳所有的列。

### [创建额外的轨道来包含元素](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#创建额外的轨道来包含元素)

可以使用网格布局定义一个显式网格，但是根据规范它会处理你加在网格外面的内容，当必要时它会自动增加行和列，它会尽可能多的容纳所有的列。

使用display:grid/inline-grid就可以是用网格

```html
<div class="wrapper">
   <div>One</div>
   <div>Two</div>
   <div>Three</div>
   <div>Four</div>
   <div>Five</div>
</div>

```

```css
.wrapper {
  display: grid;
}

```

![Using the Grid Highlighter in DevTools to view a grid](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout/1-grid-inspector.png)

我们通过 **grid-template-columns** 和 **grid-template-rows** 属性来定义网格中的行和列,这些属性定义了网格的轨道。一个网格轨道就是网格中任意两条线之间的空间。在下图中你可以看到一个高亮的轨道——网格的第一个行轨道。

```
<div class="wrapper">
   <div>One</div>
   <div>Two</div>
   <div>Three</div>
   <div>Four</div>
   <div>Five</div>
</div>
.wrapper {
  display: grid;
  grid-template-columns: 200px 200px 200px;//每一列的宽度都是200px，分为三列
}
```

### fr

f r是专门用于grid的单位，新的`fr`单位代表网格容器中可用空间的一等份。下一个网格定义将创建三个相等宽度的轨道，这些轨道会随着可用空间增长和收缩。

```
<div class="wrapper">
   <div>One</div>
   <div>Two</div>
   <div>Three</div>
   <div>Four</div>
   <div>Five</div>
</div>
.wrapper {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;//这样将等分为三分
}
.wrapper {
  display: grid;
  grid-template-columns: 2fr 1fr 1fr;//这样就是分为四分，其中第一个div站两份
}
.wrapper {
  display: grid;
  grid-template-columns: 500px 1fr 2fr;//还可以和其他单位混用，第一个是固定单位，其他剩余的等分
}

```

### repeat

```
.wrapper {
  display: grid;
  grid-template-columns: 1fr 1fr 1fr;
}

```

也可以写成

```
.wrapper {
  display: grid;
  grid-template-columns: repeat(3, 1fr);//1fr重复3次，就是三等分
}

```

Repeat 语句可以用于重复轨道列表中的一部分。在下面的例子中我创建了一个网格：它起始轨道为 20 像素，接着重复了 6 个`1fr`的轨道，最后再添加了一个 20 像素的轨道。

```
.wrapper {
  display: grid;
  grid-template-columns: 20px repeat(6, 1fr) 20px;
}
```

Repeat 语句可以传入一个轨道列表，因此你可以用它来创建一个多轨道模式的重复轨道列表。在下一个例子中，网格将有共计 10 个轨道，为 1 个`1fr`轨道后面跟着 1 个`2fr`轨道，该模式重复 5 次。

```
.wrapper {
  display: grid;
  grid-template-columns: repeat(5, 1fr 2fr);
}
```

