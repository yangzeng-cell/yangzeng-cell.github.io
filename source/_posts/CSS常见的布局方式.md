---
title: CSS常见的布局方式
date: 2022-10-22 00:45:13
tags:
- [css布局]
catagories:
- [CSS,css布局]
---

# css常见的几种布局方式

## 单列布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .wrapper {
        max-width: 960px;
        margin: 0 auto;
        background-color: blue;
      }
    </style>
  </head>
  <body>
    <div class="wrapper">
      内容内容 内容内容 内容内容 内容内容 内容内容 内容内容 内容内容
    </div>
  </body>
</html>
```

单列布局是指在页面中居中布局，margin：auto 0，可以让块级元素居中显示，max-width被设置，在页面中当页面宽度大于960px时，会以max-width进行布局，当页面小于max-width时就会缩小宽度

## 两列自适应布局

### **两列自适应布局是指一列由内容撑开，另一列撑满剩余宽度的布局方式**

#### float+overflow:hidden

如果是普通的两列布局，**浮动+普通元素的margin**便可以实现，但如果是自适应的两列布局，利用**float+overflow:hidden**便可以实现，这种办法主要通过overflow触发BFC,而BFC不会重叠浮动元素。由于设置overflow:hidden并不会触发IE6-浏览器的haslayout属性，所以需要设置zoom:1来兼容IE6-浏览器。具体代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .parent {
        overflow: hidden;
        zoom: 1;
        background-color: green;
      }
      .left {
        float: left;
        margin-right: 10px;
        height: 100px;
        background-color: brown;
      }
      .right {
        overflow: hidden;
        zoom: 1;
        height: 100px;
        background-color: yellow;
      }
    </style>
  </head>
  <body>
    <div class="parent">
      <div class="left">
        <p>left</p>
      </div>
      <div class="right">
        <p>right</p>
        <p>right</p>
      </div>
    </div>
  </body>
</html>
```

**注意点:如果侧边栏在右边时，注意渲染顺序。即在HTML中，先写侧边栏后写主内容**

#### 使用flex布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .parent {
        display: flex;
        background-color: green;
      }
      .left {
        margin-right: 10px;
        height: 100px;
        background-color: brown;
      }
      .right {
        flex: 1;
        height: 100px;
        background-color: yellow;
      }
    </style>
  </head>
  <body>
    <div class="parent">
      <div class="left">
        <p>left</p>
      </div>
      <div class="right">
        <p>right</p>
        <p>right</p>
      </div>
    </div>
  </body>
</html>

```

#### 使用grid布局

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .parent {
        display: grid;
        grid-template-columns: auto 1fr;
        background-color: green;
        column-gap: 10px;
      }
      .left {
        height: 100px;
        background-color: brown;
      }
      .right {
        height: 100px;
        background-color: yellow;
      }
    </style>
  </head>
  <body>
    <div class="parent">
      <div class="left">
        <p>left</p>
      </div>
      <div class="right">
        <p>right</p>
        <p>right</p>
      </div>
    </div>
  </body>
</html>

```

## 三栏布局

三栏布局就是中间宽度可以自由缩放，两边宽度是固定的

### 圣杯布局

#### 特点

**比较特殊的三栏布局，同样也是两边固定宽度，中间自适应，唯一区别是dom结构必须是先写中间列部分，这样实现中间列可以优先加载**。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .container {
        padding-left: 220px;
        padding-right: 220px;
      }
      .left {
        float: left;
        width: 200px;
        height: 400px;
        background: red;
        margin-left: -100%;
        position: relative;
        left: -220px;
      }
      .center {
        float: left;
        width: 100%;
        height: 500px;
        background: yellow;
      }
      .right {
        float: left;
        width: 200px;
        height: 400px;
        background: blue;
        margin-left: -200px;
        position: relative;
        right: -220px;
      }
    </style>
  </head>
  <body>
    <article class="container">
      <div class="center">
        <h2>圣杯布局</h2>
      </div>
      <div class="left"></div>
      <div class="right"></div>
    </article>
  </body>
</html>

```

圣杯布局父元素需要设置中间的div的padding，使用float进行布局，当中间元素占据width：100%时，左右两边的div会被挤下去，所以需要使用margin，和position进行定位

#### 实现步骤

- 三个部分都设定为左浮动，**否则左右两边内容上不去，就不可能与中间列同一行**。然后设置center的宽度为100%(**实现中间列内容自适应**)，此时，left和right部分会跳到下一行

![img](https://pic1.zhimg.com/v2-4f1da13dbb42ee61e7986fe27bca776c_r.jpg)

- 通过设置margin-left为负值让left和right部分回到与center部分同一行

![img](https://pic4.zhimg.com/80/v2-6d1a7a6762acbe5ab9c315d01554031b_1440w.webp)

- 通过设置父容器的padding-left和padding-right，让左右两边留出间隙。

![img](https://pic3.zhimg.com/80/v2-c7a36f977b49d4fa46f0f5a9782c2be6_1440w.webp)

- 通过设置相对定位，让left和right部分移动到两边。

![img](https://pic3.zhimg.com/80/v2-b7e5a97b8d57e023ee457dc13f0206d2_1440w.webp)

#### 缺点

- center部分的最小宽度不能小于left部分的宽度，否则会left部分掉到下一行
- 如果其中一列内容高度拉长(如下图)，其他两列的背景并不会自动填充。(借助等高布局正padding+负margin可解决，下文会介绍)

![img](https://pic3.zhimg.com/80/v2-8bb2a97acd1f52376d69638faf64c4a2_1440w.webp)

### 双飞翼布局

#### 特点

**同样也是三栏布局，在圣杯布局基础上进一步优化，解决了圣杯布局错乱问题，实现了内容与布局的分离。而且任何一栏都可以是最高栏，不会出问题**。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .container {
        min-width: 600px;
      }
      .left {
        float: left;
        width: 200px;
        height: 400px;
        background: red;
        margin-left: -100%;
      }
      .center {
        float: left;
        width: 100%;
        height: 500px;
        background: yellow;
      }
      .center .inner {
        margin: 0 200px; //新增部分
      }
      .right {
        float: left;
        width: 200px;
        height: 400px;
        background: blue;
        margin-left: -200px;
      }
    </style>
  </head>
  <body>
    <article class="container">
      <div class="center">
        <div class="inner">双飞翼布局</div>
      </div>
      <div class="left"></div>
      <div class="right"></div>
    </article>
  </body>
</html>

```

#### 实现步骤(前两步与圣杯布局一样)

- 三个部分都设定为左浮动，然后设置center的宽度为100%，此时，left和right部分会跳到下一行；
- 通过设置margin-left为负值让left和right部分回到与center部分同一行；
- center部分增加一个内层div，并设margin: 0 200px；

#### 缺点

**多加一层 dom 树节点，增加渲染树生成的计算量**。

#### 两种布局实现方式对比:

- 两种布局方式都是把主列放在文档流最前面，使主列优先加载。
- 两种布局方式在实现上也有相同之处，都是让三列浮动，然后通过负外边距形成三列布局。
- 两种布局方式的不同之处在于如何处理中间主列的位置： **圣杯布局是利用父容器的左、右内边距+两个从列相对定位**； **双飞翼布局是把主列嵌套在一个新的父级块中利用主列的左、右外边距进行布局调整**

## 等高布局

等高布局是指子元素在父元素中高度相等的布局方式。接下来我们介绍常见几种实现方式：

### 利用正padding+负margin

我们通过等布局便可解决圣杯布局的第二点缺点，因为背景是在 padding 区域显示的，**设置一个大数值的 padding-bottom，再设置相同数值的负的 margin-bottom，并在所有列外面加上一个容器，并设置 overflow:hidden 把溢出背景切掉**。这种可能实现多列等高布局，并且也能实现列与列之间分隔线效果，结构简单，兼容所有浏览器。新增代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .container {
        padding-left: 220px;
        padding-right: 220px;
      }
      .left {
        float: left;
        width: 200px;
        height: 400px;
        background: red;
        margin-left: -100%;
        position: relative;
        left: -220px;
      }
      .center {
        float: left;
        width: 100%;
        height: 500px;
        background: yellow;
      }
      .right {
        float: left;
        width: 200px;
        height: 400px;
        background: blue;
        margin-left: -200px;
        position: relative;
        right: -220px;
      }
      .center,
      .left,
      .right {
        padding-bottom: 10000px;
        margin-bottom: -10000px;
      }
      .container {
        padding-left: 220px;
        padding-right: 220px;
        /* 把溢出背景切掉 */
        overflow: hidden;
      }
    </style>
  </head>
  <body>
    <article class="container">
      <div class="center">
        <h2>等高布局</h2>
      </div>
      <div class="left"></div>
      <div class="right"></div>
    </article>
  </body>
</html>
```

### 利用背景图片

这种方法是我们实现等高列最早使用的一种方法，就是使用背景图片，在列的父元素上使用这个背景图进行Y轴的铺放，从而实现一种等高列的假象。实现方法简单，兼容性强，不需要太多的css样式就可以轻松实现,但此方法不适合流体布局等高列的布局。

在制作样式之前需要一张类似下面的背景图：

![img](https://pic1.zhimg.com/80/v2-c4a4a0bfe32023c4773ee8f10e3918fc_1440w.webp)

```html
<div class=”container clearfix”>
    <div class=”left”></div>
    <div  class=”content”></div>
    <div class=”right”></div>
</div>
.container {
  background: url("column.png") repeat-y;
  width: 960px;
  margin: 0 auto;
}
.left {
  float: left;
  width: 220px;
}
.content {
  float: left;
  width: 480px;
}
.right {
  float: left;
  width: 220px;
}
```

### 模仿表格布局

这是一种非常简单，易于实现的方法。不过兼容性不好，在ie6-7无法正常运行。

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      .table {
        width: auto;
        min-width: 1000px;
        margin: 0 auto;
        padding: 0;
        display: table;
      }
      .tableRow {
        display: table-row;
      }
      .tableCell {
        display: table-cell;
        width: 33%;
      }
      .cell1 {
        background: #f00;
        height: 800px;
      }
      .cell2 {
        background: #0f0;
      }
      .cell3 {
        background: #00f;
      }
    </style>
  </head>
  <body>
    <div class="container table">
      <div class="containerInner tableRow">
        <div class="column tableCell cell1">
          <div class="left aside">....</div>
        </div>
        <div class="column tableCell cell2">
          <div class="content section">...</div>
        </div>
        <div class="column tableCell cell3">
          <div class="right aside">...</div>
        </div>
      </div>
    </div>
  </body>
</html>

```

### 使用边框和定位

这种方法是使用边框和绝对定位来实现一个假的高度相等列的效果。结构简单，兼容各浏览器，容易掌握。假设你需要实现一个两列等高布局，侧栏高度要和主内容高度相等。

```css
#wrapper {
  width: 960px;
  margin: 0 auto;
}
#mainContent {
  border-right: 220px solid #dfdfdf;
  position: absolute;
  width: 740px;
  height: 800px;  
  background: green;
}
#sidebar {
  background: #dfdfdf;
  margin-left: 740px;
  position: absolute;
  height: 800px;
  width: 220px;
}
<div id="wrapper">
    <div id="mainContent">...</div>
    <div id="sidebar">...</div>
</div>
```

## 粘连布局

### 特点

- 有一块内容`<main>`，当`<main>`的高康足够长的时候，紧跟在`<main>`后面的元素`<footer>`会跟在`<main>`元素的后面。
- 当`<main>`元素比较短的时候(比如小于屏幕的高度),我们期望这个`<footer>`元素能够“粘连”在屏幕的底部

![img](https://pic1.zhimg.com/80/v2-08f08fceaca93e547a19441984cc8720_1440w.webp)

具体代码如下：

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
    <style>
      * {
        margin: 0;
        padding: 0;
      }
      html,
      body {
        /* //高度一层层继承下来 */
        height: 100%;
      }
      #wrap {
        min-height: 100%;
        background: pink;
        text-align: center;
        overflow: hidden;
      }
      #wrap .main {
        padding-bottom: 50px;
      }
      #footer {
        height: 50px;
        line-height: 50px;
        background: deeppink;
        text-align: center;
        margin-top: -50px;
      }
    </style>
  </head>
  <body>
    <div id="wrap">
      <div class="main">
        main <br />
        main <br />
        main <br />
      </div>
    </div>
    <div id="footer">footer</div>
  </body>
</html>
```



### 实现步骤

### (1)footer必须是一个独立的结构，与wrap没有任何嵌套关系

### (2)wrap区域的高度通过设置min-height，变为视口高度

### (3)footer要使用margin为负来确定自己的位置

### (4)在main区域需要设置 padding-bottom。这也是为了防止负 margin 导致 footer 覆盖任何实际内容。
