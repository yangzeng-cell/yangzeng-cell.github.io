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

## [`grid-column` 和 `grid-row` 的简写](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid#grid-column_和_grid-row_的简写)

grid-column-start和grid-column-end的缩写是grid-column，grid-row-start和grid-row-end的缩写

```
<div class="wrapper">
   <div class="box1">One</div>
   <div class="box2">Two</div>
   <div class="box3">Three</div>
   <div class="box4">Four</div>
</div>

```

```
.box1 {
   grid-column: 1 / 2;
   grid-row: 1 / 4;
}
.box2 {
   grid-column: 3 / 4;
   grid-row: 1 / 3;
}
.box3 {
   grid-column: 2 / 3;
   grid-row: 1 /  2;
}
.box4 {
   grid-column: 2 / 4;
   grid-row: 3 / 4;
}

```

<iframe class="sample-code-frame" title="grid-column 和 grid-row 的简写 sample" id="frame_grid-column_和_grid-row_的简写" width="300" height="330" src="https://yari-demos.prod.mdn.mozit.cloud/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid/_sample_.grid-column_%E5%92%8C_grid-row_%E7%9A%84%E7%AE%80%E5%86%99.html" loading="lazy"></iframe>

## [默认跨度](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid#默认跨度)

实际上如果一个元素只延伸一个轨道的话，你可以省略 `grid-column-end` 或 `grid-row-end` 值。元素默认延伸一个轨道

### [默认跨度的普通写法](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid#默认跨度的普通写法)

我们最初的示例的普通写法会是这样：

```
<div class="wrapper">
   <div class="box1">One</div>
   <div class="box2">Two</div>
   <div class="box3">Three</div>
   <div class="box4">Four</div>
</div>
```

```
.box1 {
   grid-column-start: 1;
   grid-row-start: 1;
   grid-row-end: 4;
}
.box2 {
   grid-column-start: 3;
   grid-row-start: 1;
   grid-row-end: 3;
}
.box3 {
   grid-column-start: 2;
   grid-row-start: 1;
}
.box4 {
   grid-column-start: 2;
   grid-column-end: 4;
   grid-row-start: 3;
}
```

### [默认跨度的简写](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid#默认跨度的简写)

```
<div class="wrapper">
   <div class="box1">One</div>
   <div class="box2">Two</div>
   <div class="box3">Three</div>
   <div class="box4">Four</div>
</div>
```

```
.box1 {
   grid-column: 1 ;//可以省略第二个值
   grid-row: 1 / 4;
}
.box2 {
   grid-column: 3 ;
   grid-row: 1 / 3;
}
.box3 {
   grid-column: 2 ;
   grid-row: 1 ;
}
.box4 {
   grid-column: 2 / 4;
   grid-row: 3 ;
}
```

<iframe class="sample-code-frame" title="默认跨度的简写 sample" id="frame_默认跨度的简写" width="300" height="330" src="https://yari-demos.prod.mdn.mozit.cloud/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid/_sample_.%E9%BB%98%E8%AE%A4%E8%B7%A8%E5%BA%A6%E7%9A%84%E7%AE%80%E5%86%99.html" loading="lazy"></iframe>

## [`grid-area` 属性](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid#grid-area_属性)

我们可以更进一步，给每个元素只定义一个属性 [`grid-area`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/grid-area)。值的顺序如下

- grid-row-start
- grid-column-start
- grid-row-end
- grid-column-end

```
<div class="wrapper">
   <div class="box1">One</div>
   <div class="box2">Two</div>
   <div class="box3">Three</div>
   <div class="box4">Four</div>
</div>
.box1 {
   grid-area: 1 / 1 / 4 / 2;
}
.box2 {
   grid-area: 1 / 3 / 3 / 4;
}
.box3 {
   grid-area: 1 / 2 / 2 / 3;
}
.box4 {
   grid-area: 3 / 2 / 4 / 4;
}
```

`grid-area` 的值的顺序看起来可能有点奇怪，比如说它正好和定义 margin 和 padding 的简写的值的顺序相反。但如果说是因为它与 CSS 书写模式规范中的书写方向相关就不难理解了。我们会在之后的文章中探讨网格与书写模式的关系，但我们有 4 个书写流关联的方向：

- 块起始（block-start）
- 块结束（block-end）
- 行起始（inline-start）
- 行结束（inline-end）

## [反方向计数](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid#反方向计数)

我们也可以从行和块结束线开始反方向计数，对于英语来说就是右端的列线和底端的行线。这些线会被记为 `-1`，然后你可以从这往前数，所以倒数第 2 条线会被记为 `-2`。值得注意的是最后一条线是指显式定义网格的最后一条线，即由 `grid-template-columns` 和 `grid-template-rows` 定义的网格，并不把隐式定义网格的加入的行和列纳入考虑。

下面这个示例中，我们通过从右端和底端开始定义布局，把之前的示例的布局翻转了。

```
<div class="wrapper">
   <div class="box1">One</div>
   <div class="box2">Two</div>
   <div class="box3">Three</div>
   <div class="box4">Four</div>
</div>
.box1 {
   grid-column-start: -1;
   grid-column-end: -2;
   grid-row-start: -1;
   grid-row-end: -4;
}
.box2 {
   grid-column-start: -3;
   grid-column-end: -4;
   grid-row-start: -1;
   grid-row-end: -3;
}
.box3 {
   grid-column-start: -2;
   grid-column-end: -3;
   grid-row-start: -1;
   grid-row-end: -2;
}
.box4 {
   grid-column-start: -2;
   grid-column-end: -4;
   grid-row-start: -3;
   grid-row-end: -4;
}
```

<iframe class="sample-code-frame" title="反方向计数 sample" id="frame_反方向计数" width="300" height="330" src="https://yari-demos.prod.mdn.mozit.cloud/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid/_sample_.%E5%8F%8D%E6%96%B9%E5%90%91%E8%AE%A1%E6%95%B0.html" loading="lazy" style="box-sizing: content-box; border: 1px solid var(--border-primary); max-width: 100%; width: calc((100% - 2rem) - 2px); background: rgb(255, 255, 255); border-radius: var(--elem-radius); padding: 1rem;"></iframe>

### [使元素跨越整个网格](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid#使元素跨越整个网格)

拥有从开始计数和从末尾计数这两种定位方法使得使一个元素跨越整个网格变得很方便：

```
.item {
    grid-column: 1 / -1;
}
```

## [间距](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid#间距)

CSS 网格规范加入了用 [`column-gap`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/column-gap) 和 [`row-gap`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/row-gap) 属性定义列间距和行间距的能力。这两个属性的行为和多列布局中的 [`column-gap`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/column-gap) 属性很像。

**备注：** 在浏览器首次引入网格属性 [`column-gap`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/column-gap)、[`row-gap`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/row-gap) 和 [`gap`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/gap) 时，具有 `grid-` 前缀。分别是 `grid-column-gap`、`grid-row-gap` 和 `grid-gap`。

浏览器正在更新它们的渲染引擎，以删除这一前缀，但是携带有前缀的版本将以别名的形式进行维护，使得它们可以被安全地使用。

间距只出现在网格轨道与轨道之间，它们并不会出现在网格容器的四周。通过在网格容器上定义这些属性，我们给上例加上了间距：

```
<div class="wrapper">
   <div class="box1">One</div>
   <div class="box2">Two</div>
   <div class="box3">Three</div>
   <div class="box4">Four</div>
</div>
```

```
.box1 {
    grid-column: 1 ;
    grid-row: 1 / 4;
}
.box2 {
    grid-column: 3 ;
    grid-row: 1 / 3;
}
.box3 {
    grid-column: 2 ;
    grid-row: 1 ;
}
.box4 {
    grid-column: 2 / 4;
    grid-row: 3 ;
}
.wrapper {
    display: grid;
    grid-template-columns: repeat(3, 1fr);
    grid-template-rows: repeat(3, 100px);
    column-gap: 20px;
    row-gap: 1em;
}
```

<iframe class="sample-code-frame" title="间距 sample" id="frame_间距" width="300" height="350" src="https://yari-demos.prod.mdn.mozit.cloud/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid/_sample_.%E9%97%B4%E8%B7%9D.html" loading="lazy" style="box-sizing: content-box; border: 1px solid var(--border-primary); max-width: 100%; width: calc((100% - 2rem) - 2px); background: rgb(255, 255, 255); border-radius: var(--elem-radius); padding: 1rem;"></iframe>

间距可以使用gap进行简写

## [使用 `span` 关键字](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid#使用_span_关键字)

除了”起始线与结束线“的定位方法，你还可以使用”起始线与跨越轨道数量“的定位方法

```
<div class="wrapper">
   <div class="box1">One</div>
   <div class="box2">Two</div>
   <div class="box3">Three</div>
   <div class="box4">Four</div>
</div>
.box1 {
    grid-column: 1;
    grid-row: 1 / span 3;
}
.box2 {
    grid-column: 3;
    grid-row: 1 / span 2;
}
.box3 {
    grid-column: 2;
    grid-row: 1;
}
.box4 {
    grid-column: 2 / span 2;
    grid-row: 3;
}
```

<iframe class="sample-code-frame" title="使用 span 关键字 sample" id="frame_使用_span_关键字" width="300" height="330" src="https://yari-demos.prod.mdn.mozit.cloud/zh-CN/docs/Web/CSS/CSS_Grid_Layout/Line-based_Placement_with_CSS_Grid/_sample_.%E4%BD%BF%E7%94%A8_span_%E5%85%B3%E9%94%AE%E5%AD%97.html" loading="lazy" style="box-sizing: content-box; border: 1px solid var(--border-primary); max-width: 100%; width: calc((100% - 2rem) - 2px); background: rgb(255, 255, 255); border-radius: var(--elem-radius); padding: 1rem; color: rgb(27, 27, 27); font-family: Inter, &quot;system-ui&quot;, &quot;Segoe UI&quot;, Roboto, Oxygen, Ubuntu, Cantarell, &quot;Fira Sans&quot;, &quot;Droid Sans&quot;, &quot;Helvetica Neue&quot;, sans-serif; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; white-space: normal; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"></iframe>

你也可以在 `grid-row-start`/`grid-row-end` 和 `grid-column-start`/`grid-column-end` 属性中使用 `span` 关键字。接下来两个例子会创建同样的网格。第一个例子中我们设定了起始行，然后说我们想结束线在跨越 3 条线之后。那这个元素就会从 1 号线开始，跨越 3 条线，到 4 号线结束。

```
.box1 {
    grid-column-start: 1;
    grid-row-start: 1;
    grid-row-end: span 3;
}
```

第二个例子中，我们定义了结束行线，然后设置起始线为跨越 3 条线。意味着这个元素会从指定的线往上跨越 3 条线。这个元素会从 4 号线开始，跨越 3 条线到 1 号线。

```
.box1 {
    grid-column-start: 1;
    grid-row-start: span 3;
    grid-row-end: 4;
}
```
