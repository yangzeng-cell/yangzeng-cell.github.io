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

### grid-auto-rows和grid-auto-columns

```html
<div class="wrapper">
   <div>One</div>
   <div>Two</div>
   <div>Three</div>
   <div>Four</div>
   <div>Five</div>
</div>
.wrapper {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-auto-rows: 200px;
}
```

Grid-auto-rows是用来定义每一行的默认高度，或者定义1fr的宽度，grid-auto-columns也是一样

网格用 minmax() 函数来解决这个问题。在下一个例子中我用 minmax() 作为 grid-auto-rows 的值。自动创建的行高将会是最小 100 像素，最大为 auto。用 auto 意味着行的尺寸将会根据内容的大小来自动变换：根据本行中最高的单元，把空间扩展到足够容纳该单元

```
.wrapper {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  grid-auto-rows: minmax(100px, auto);//还可以使用minax来定义每个轨道的区间，这个定义的是最小值为100px，最大值为auto，可以由内容撑开
}
```

```
<div class="wrapper">
  <div>One</div>
  <div>Two
    <p>I have some more content in.</p>
    <p>This makes me taller than 100 pixels.</p>
  </div>
  <div>Three</div>
  <div>Four</div>
  <div>Five</div>
</div>

```

<iframe class="sample-code-frame" title="轨道大小和minmax()" id="frame_轨道大小和_minmax" width="230" height="490" src="https://yari-demos.prod.mdn.mozit.cloud/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout/_sample_.%E8%BD%A8%E9%81%93%E5%A4%A7%E5%B0%8F%E5%92%8C_minmax.html" loading="lazy"></iframe>

### [跨轨道放置网格元素](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#跨轨道放置网格元素)

grid-column-start：是用于从哪条列网格线开始，开始的索引是1，而不是0

grid-column-end：是从哪一条列网格线结束

grid-row-start：从哪条线开始行

grid-row-end：结束行

```
<div class="wrapper">
   <div class="box1">One</div>
   <div class="box2">Two</div>
   <div class="box3">Three</div>
   <div class="box4">Four</div>
   <div class="box5">Five</div>
</div>

```

```
.wrapper {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-auto-rows: 100px;
}
.box1 {
    grid-column-start: 1;//从第一条列线开始
    grid-column-end: 4;//到第4条列线结束
    grid-row-start: 1;//从第一条航线开始
    grid-row-end: 3;//到第三条航线结束
}
.box2 {
    grid-column-start: 1;
    grid-row-start: 3;
    grid-row-end: 5;
}
```

<iframe class="sample-code-frame" title="跨轨道放置网格元素 sample" id="frame_跨轨道放置网格元素" width="220" height="410" src="https://yari-demos.prod.mdn.mozit.cloud/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout/_sample_.%E8%B7%A8%E8%BD%A8%E9%81%93%E6%94%BE%E7%BD%AE%E7%BD%91%E6%A0%BC%E5%85%83%E7%B4%A0.html" loading="lazy"></iframe>

## [网格单元](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#网格单元)

网格单元就是指最小的网格单位

![The first cell of the grid highlighted](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout/1_grid_cell.png)

## [网格区域](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#网格区域)

是指每一个所占的区域

![A grid area](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout/1_grid_area.png)

## [网格间距](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#网格间距)

是指每个网格之间间距

column-gap：指的是两列之间的间距

row-gap：指两行之间的间距

gap：是row-gap和column-gap的缩写

```
.wrapper {
   display: grid;
   grid-template-columns: repeat(3, 1fr);
   column-gap: 10px;
   row-gap: 1em;
}
<div class="wrapper">
   <div>One</div>
   <div>Two</div>
   <div>Three</div>
   <div>Four</div>
   <div>Five</div>
</div>

```

<iframe class="sample-code-frame" title="网格间距 sample" id="frame_网格间距" src="https://yari-demos.prod.mdn.mozit.cloud/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout/_sample_.%E7%BD%91%E6%A0%BC%E9%97%B4%E8%B7%9D.html" loading="lazy"></iframe>

间距使用的空间会在 使用弹性长度 fr 的轨道的空间计算前就被留出来，间距的尺寸定义行为和普通轨道一致，但不同的是你不能向其中插入任何内容。从以基线定位的角度来说，间距就像一条很宽的基线。

## [嵌套网格](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#嵌套网格)

```
<div class="wrapper">
    <div class="box box1">
        <div class="nested">a</div>
        <div class="nested">b</div>
        <div class="nested">c</div>
    </div>
    <div class="box box2">Two</div>
    <div class="box box3">Three</div>
    <div class="box box4">Four</div>
    <div class="box box5">Five</div>
</div>
```

![Nested grid in flow](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout/1_nested_grids_in_flow.png)

如果把 `box1` 设置成 `display: grid` 你可以给它定义轨道然后它也会变成一个网格元素，它的子级元素也会排列在这个新网格元素中。

```
.box1 {
   grid-column-start: 1;
   grid-column-end: 4;
   grid-row-start: 1;
   grid-row-end: 3;
   display: grid;
   grid-template-columns: repeat(3, 1fr);
}
```

<iframe class="sample-code-frame" title="嵌套网格 sample" id="frame_嵌套网格" width="600" height="410" src="https://yari-demos.prod.mdn.mozit.cloud/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout/_sample_.%E5%B5%8C%E5%A5%97%E7%BD%91%E6%A0%BC.html" loading="lazy"></iframe>

## [使用`z-index`控制层级](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#使用z-index控制层级)

多个网格项目可以占用同一个网格单位。如果我们回到之前根据网格线编号放置网格项目的话，我们可以更改此项来使两个网格项目重叠。

```
<div class="wrapper">
   <div class="box box1">One</div>
   <div class="box box2">Two</div>
   <div class="box box3">Three</div>
   <div class="box box4">Four</div>
   <div class="box box5">Five</div>
</div>
```

```
.wrapper {
   display: grid;
   grid-template-columns: repeat(3, 1fr);
   grid-auto-rows: 100px;
}
.box1 {
   grid-column-start: 1;
   grid-column-end: 4;
   grid-row-start: 1;
   grid-row-end: 3;
}
.box2 {
   grid-column-start: 1;
   grid-row-start: 2;
   grid-row-end: 4;
}

```

<iframe class="sample-code-frame" title="使用z-index控制层级" id="frame_l_ex" width="210" height="410" src="https://yari-demos.prod.mdn.mozit.cloud/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout/_sample_.l_ex.html" loading="lazy"></iframe>

网格项目 `box2` 现在覆盖于 `box1 之上，其覆盖顺序遵循文档流的原始顺序（后来居上）`

### [控制顺序](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout#控制顺序)

我们可以在网格项目发生重叠时使用 `z-index` 属性控制重叠的顺序——就像放置网格项目一样。如果我们给 `box2` 设定一个低于 `box1` 的 `z-index` 值的话，`box2` 将会显示在 box1 的下方。

```
.wrapper {
   display: grid;
   grid-template-columns: repeat(3, 1fr);
   grid-auto-rows: 100px;
}
.box1 {
   grid-column-start: 1;
   grid-column-end: 4;
   grid-row-start: 1;
   grid-row-end: 3;
   z-index: 2;
}
.box2 {
   grid-column-start: 1;
   grid-row-start: 2;
   grid-row-end: 4;
   z-index: 1;
}

```

<iframe class="sample-code-frame" title="控制顺序 sample" id="frame_控制顺序" width="210" height="410" src="https://yari-demos.prod.mdn.mozit.cloud/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Basic_Concepts_of_Grid_Layout/_sample_.%E6%8E%A7%E5%88%B6%E9%A1%BA%E5%BA%8F.html" loading="lazy"></iframe>
