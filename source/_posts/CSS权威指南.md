---
title: CSS权威指南
date: 2022-11-30 22:47:43
tags:
- [CSS]
categories:
- [CSS,CSS权威指南]
---



## 置换元素和非置换元素

置换元素是指用来置换元素内容的部分不由文档内容直接表示，在html中，最常见的置换元素是img，input

非置换元素即元素的内容由用户代理通常是浏览器在元素自身生成的框中显示，文本，段落，标题，单元格，列表等其他都是非置换元素

## 块级元素和行内元素

块级元素是独占一行，行内元素不会独占一行，在html中，块级元素不能出现在行内元素中，但是css不会限制他们的显示方式，可以互相嵌套

## 把CSS应用到HTML的方式

### link标签

```
<link rel="stylesheet" type="text/css" href="basic.css">
```

多个link，会自动合成一个link

### style元素

```
<style type="test/css"></style>
```

style中可以使用@import导入外部样式

### @import指令

@import也可以引入外部样式，但是@import必须放在顶部，否则不起作用

可以使用多个@import

### HTTP链接

### 行内样式

## CSS注释

```
/* 这是一段注释 */
```

## 媒体查询

## 选择器

### 元素选择器

主要是指html元素

### 群组选择器

```
h1,p{color:red}
```

### 类选择器

```
.warning{font-weight:bold}
```

### 交集选择器

```
p.wraning.help{background:red}
```

### id选择器

id选择器使用不能超过一次，id选择器的优先级高于类选择器

```
#lead{font-weight:bold}
```

### 简单属性描述符

```
h1[class]{color:silver};  在h1标签中有class属性的都设置color

*[title]{font-weight:bold} ;  所有有title属性的font-weight都设置为bold

a[title][href]{font-weight:bold}; a标签中存在title和href属性的font-weight都设置为bold
```

### 精准的属性描述符

```
a[href="http://google.com"]{font-weight:bold}

a[href="http://google.com"][title="google"]{font-weight:bold}
```

### 使用属性选择器匹配子串

```
[foo|='bar'] 选择的元素有foo属性，且以bar和-开头，或者是bar本身 例如 foo="bar-gerr"

[foo~="bar"] 选择的元素有foo属性，且其值包含bar这个词的一组词，就是匹配以空格分隔的一组词的一个  例如foo="bar li"

[foo*="bar"] 选择的元素有foo属性，且其值包含子串bar 例如foo=“bary”

[foo^="bar"] 选择的元素有foo属性，且其值是以bar开头  

[foo$="bar"] 选在的元素有foo属性，且其值是以bar结尾
```

### 不区分大小写的标识符

```
a[href$='.PDF' i]
```

### 后代选择器

```
.sidebar a:link{color:white} //为所有符合条件的后代设置样式，无论层级多深
```

### 子代选择器

```
h1 > strong {color:red}  //只为子元素设置样式
```

### 相邻兄弟选择器

```
h1 + p {margin-top:0;}  //选择紧跟在h1后面的p元素，他们是同属于一个父元素
```

### 后继同胞选择器

```
h2 ~ol{font-style:italic;}  //选择h2中同属于一个父元素的所有兄弟ol，和相邻兄弟选择器不同的是，相邻兄弟选择器只能匹配一个
```

### 伪类选择器

```
a:link:hover{color:red;}  //鼠标悬停在未访问过的链接的时候

a:visited:hover{color:maroon;}  //鼠标悬停在已访问过的链接的时候。
```

这种伪类选择器的使用和伪类的顺序无关，但是不要使用互相排斥的伪类类如`a:link:visited`

#### 结构伪类

```
:root{border:1px solid red}  选择文档根元素，在html中，根元素始终是html元素，在xml中是不一样的

:empty伪类可以选择没有任何子代的元素，甚至连文本结点都没有包括空白文本，元素里面只有注释也是属于空元素

*:empty{display:none;}可以匹配所有的空元素，	但是这里有个陷阱，他可以匹配img，input，还有匹配没有内容的textarea
:empty是唯一考虑文本结点的css选择器
```

选择唯一子代

```
a[href] img:only-child  //有href属性的a标签中 有唯一的子后代元素img  文本不属于元素

在后代选择器中使用:only-child,列出的元素不一定是父子关系

例如
<a href><img/></a>
<a href><p><img/></p></a>
<a href><p><p><img/></p></p></a>
都可以匹配到

如果需要确保是父子关系的话必须使用>
a[href] > img:only-child

如果想匹配超链接中唯一的img，但是链接中还有其他内容，img不是a标签中的唯一子元素
可以使用:only-of-type
a[href] img:only-of-type {border:5px solid black;}

<a href="#"><span>hello</span><img/></a>

only-of-type和last-child的区别是only-of-type会忽略其他子元素，只会匹配对应的元素，only-child只会匹配唯一的元素

注意:only-of-type指的是元素，不能用于其他
例如：
p.unique:only-of-type {color:red;}

<div>
	<p class="unique">this is paragraph</p>
	<p>this is paragraph</p>
</div>
这两个段落都不会被匹配，因为都是div的后代，不是唯一的类型，:only-of-type前面必须是元素类型
```

选择第一个和最后一个子代

:first-child 选择一个元素的第一个子元素

```
p:first-child {font-weight:bold;}
li:first-child {text-transform:uppercase;}
```

人们对p:first-child最大的误解就是认为他是选择p标签下的第一个子元素，如果想选择一个元素的第一个em子元素，应该是em:first-child,和:first-child对应的还有:last-child

选择第一个或者最后一个的某个元素

```
:first-of-type
:last-of-type

table:only-of-type {background:color}  等价于 table:first-of-type:last-of-type {background:color}
```

选择每第n个元素

```
:first-child等价于:nth-child(1)

:nth-child(an+b) n=0,1,2,3,4..  元素是从1开始算起的，没有第0个元素，例如3n，则0，3，6，9，0是匹配不到的

:nth-child(2n+1)等价于 :nth-child(odd)
:nth-child(2n)等价于 :nth-child(even)
tr:nth-child(n+9)等价于 tr:nth-child(8) ~tr

:nth-last-child是从后面开始算起的

:nth-child(1):nth-last-child(1)等价于:only-child

注意：nth-child(an+b)和：nth-of-type(an+b)的区别
```

### 超链接伪类

```
:link 指未访问过的地址
:visited 指已访问过的地址
```

### 用户操作伪类

```
:focus 指当前获得输入焦点的元素，即可以接受键盘输入或者以某种方式激活
:hover 指鼠标放置在上面的元素，例如鼠标悬停在超链接上
:active 指用户输入激活的元素，例如用户单击超链接，按下鼠标的那段时间
```

可以处于:active状态的元素有链接，菜单项目，以及可以设定taindex属性的元素，这些元素包括其他交互元素如表单控件，可编辑内容的元素，还可以获得焦点，这些伪类最常用于超链接

这些伪类的顺序不可以随意改变

```
link-visited-focus-hover-active
```

动态伪类引起的问题

例如：如果把link，visited设置为相同的font-size，而hover和active的时候增大字体

```
a:link,a:visited {font-size:13px;}
a:hover,a:active {font-size:20px;}
```

用户在鼠标悬停或者在触屏点击的时候字体增大可能会出现重排的情况

### UI状态伪类

| 伪类           | 说明                                                         |
| -------------- | ------------------------------------------------------------ |
| :disabled      | 即指代禁用用户界面元素例如表单元素，即不接受输入元素         |
| :checked       | 指代用户或者文档默认选中的radio/checkbox                     |
| :enabled       | 指代启用的用户界面元素例如表单元素，即接受输入的元素         |
| :indeterminate | 指代既未选中耶没有未选中的单选按钮或者复选框，这个状态只能由dom来设定，不能由用户设定 |
| :default       | 指代默认选中的单选框或者复选框按钮                           |
| :valid         | 指代满足所有数据有效性语义的输入框                           |
| :invalid       | 指代不满足所有数据有效性语义的输入框                         |
| :in-range      | 指代输入的值在最小值和最大值之间的输入框                     |
| :out-of-range  | 指代输入的值小于控件允许的最小值或大于控件允许的最大值的输入框 |
| :required      | 指代必须输入值的输入框                                       |
| :optional      | 指代无需输入值的输入框                                       |
| :read-write    | 指代可由用户编辑的输入框                                     |
| :read-only     | 指代不能由用户编辑的输入框                                   |

非ui元素也可以使用:focus



:default

这个伪类通常用于上下文菜单选项，button，选择列表，目录，如果有几个同名的redio，最开始选中的那个radio就会匹配，即使用户后来选中了其他radio，页面重新加载时，也会应用于默认的那个，select元素一开始选中多个option，也可以匹配:default,:default还可以匹配button，和菜单选项

```
[type='checkbox']:default + label {font-size:italic;}

<input type="checkbox" id="chbx" checked name="foo" value="bar"/>
<label for="chbx">this is checkbox</label>
```

:required

:required是匹配必填的表单控件，required属性在html5中指定，:optional匹配没有required属性的表单控件，或者required属性为false的元素

想要提交表单，:required要求元素必须有值，:optional的有值没有值都可以

```
input:require {border:1px solid #fff;}
input:optional {border:1px solid #ccc;}

也可以这么写

input[required] {border:1px solid #fff;}
input:not([required]) {border:1px solid #ccc;}

<input type="email" placeholder="" required>
<input type="email" placeholder="" >
<input type="email" required="false" >

除了表单输入框意外，其他元素既不能必选也不能可选
```



:valid和:invalid只适用于可以检查数据有效性的元素，div是不可以的，input可能匹配其中一个，这取决于用户界面当前的状态

```css
input[type='email']:focus{
  background-position:100% 50%;
  background-repeat:no-repeat;
}
input[type='email']:focus:valid{
  background-image:url(...)
}

input[type='emial']:focus:invalid{
  background
}
```

:in-range/:out-of-range

适用于有范围要求的表单控件

```
<input type='number' min='0' max='100'>
```

```css
input[type='number']:focus {
  background-position:50% 50%;
  background-repeat:no-repeat;
}

input[type='number']:focus:valid{}
input[type='number']:focus:invalid{}
```

### :target伪类

URL中有个片段标识符，它指向的文档片段在css中称之为target，url片段标识符指向的目标元素可以使用:target伪类进行标识

例如

http://www.w3.org/tr/css3/#target-pes

target-pes这部分就是片段标识符，用#标识，对应着页面中🈶️id=“target-pes”的元素，这个元素就是片段标识符的target

利用:target,我们可以突出显示文档中的任何目标元素，或者为不同的target元素定义不同的样式，

```
*:target {border-left:5px solid gray;background:yellow url(target.png) top right no-repeat}
```

:target定义的样式在以下情况不会应用：

1.页面的url中没有片段标识符

2.页面的url中有片段标识符，但是文档中没有与之匹配的元素

如果文档中有多个元素与之匹配，样式都会应用

### :lang伪类

```
*:lang(fr) {font-style:italic;}

*:[lang|='fr'] {font-style:italic;}
```

### :not()否定伪类

```css
li:not(.moreinfo){font-style:italic;}

.link:not(li):not(p) {font-style:italic;}
```

### 伪元素选择器

伪元素和伪类的区别是伪类是：伪元素是：：

所有的伪元素选择器只能出现在选择器的最后，`p::first-line em` 这样是不允许的

::first-letter

用于修饰任何非行内元素的首字母，或者开头的标点符号或者首字母

```css
p::first-letter {color:red;}
```

::first-letter常用于实现排版效果的首字母大写或者首字母下沉，你可以把每个p元素首字母的字号设置为其余内容的两倍大，不过最好应用于第一段文字的首字母上

```
p:first-of-type::first-letter {font-size:200%;}
```

::first-line

用于修饰元素的首行文本，例如把首行文本字体变大

```css
p::first-line{
font-size:150%;
color:purple;
}
```

**对于::first-letter和::first-line的限制**

目前这两个伪元素只能用于块级元素上，不能用于行内元素，对于在::first-letter

| ::first-letter   | ::first-line     |
| ---------------- | ---------------- |
| 所有字体属性     | 所有字体属性     |
| 所有背景属性     | 所有背景属性     |
| 所有文本修饰属性 | 所有外边距属性   |
| 所有行内排版属性 | 所有内边距属性   |
| 所有行内布局属性 | 所有边框属性     |
| 所有边框属性     | 所有文本装饰属性 |
| box-shadow       | 所有行内排版属性 |
| color            | color            |
| opacity          | opacity          |

::before和::after

## 特指度

选择器的特指度是由选择器本身的组成来决定的，一个特指度值由四部分组成，0，0，0，0

选择器中的id属性值加0，1，0，0

选择器中的每个类属性，属性选择器，或者伪类加0010

选择器中的每个元素和伪元素加0001

连接符和通用选择器不加特指度



通用选择器的特指度为0000

连接符没有特指度

id选择器和选择id属性的属性选择器在特指度上是有区别的

例如

```css
html > body table tr[id='totals'] td ul >li {colors:maroon;} //0017
li#answer {color:navy;}  //0101	
```

行内样式的特指度为1000；

!important 会超多其他声明，但是不会改变特指度

## 继承

继承指的是某些样式不仅会应用到所指的元素，还会应用到元素的后代

属性值不会向上传播，即元素的样式绝不会传给祖先元素

在html中，向上传播的规则又个例外，应用在body上的背景样式会传给html元素，htm是文档的根元素，用于定义渲染的画布，这种例外仅发生在body元素定义了背景而html元素没有定义背景的情况

border属性，盒模型的属性包括margin，padding，background，border不继承

零特指度战胜没有特指度

通用选择器会应用于所有元素，	而且特指度为零，所以他声明的color会覆盖继承的color，因为继承没有特指度，通用选择器可以匹配任何选择器，所有他会覆盖继承的值，所以不要滥用通用选择器

## 层叠

css使用层叠机制把样式组合在一起，即结合继承和特指度的一些规则，css层叠的规则如下

找到匹配特定元素的所有规则

按显式权重排序应用到特定元素上的所有声明，以！important声明的规则比没有的高

按来源排序应用到特定元素上的所有声明，声明有三个来源，创作人员，读者，用户代理，创作者编写的样式优先级高于读者的样式，读者中！important声明的样式权重比其他样式高，包括创作者以！important编写的样式，创作者和读者编写的样式会覆盖用户代理编写的样式

按特指度排序的应用到特定元素上的所有声明，样式表或文档中靠后的声明权重较高，导入的样式声明会放在所有样式声明的最前面。

**按权重和来源排序**

如果两个规则应用在同一个元素上，有!important的会胜出

:如果都定义了!important,!important在行内样式的会胜出

如果显式权重相同，就要考虑来源，如果匹配元素的两个样式权重相同，而一个是创作者编写的样式，另外一个是读者编写的样式，最后创作者编写的样式会生效

```
p em {color:red}  //创作者编写
p em {color:red}  //读者编写
```

在声明的权重上，要考虑五个方面，下面按权重从高到低列出

1.读者提供的样式以!important声明

2.创作者编写的样式以!important声明

3.创作者编写的常规样式

4.读者提供的常规样式

5.用户代理提供的样式

**按特指度排序**

如果应用在一个元素上的声明有冲突，而且各个声明的显式权重和来源相同，那么因该按特指度排序，特指度最高的声明胜出。

**按前后顺序排序**

如果两个规则的显式权重，来源，和特指度都相同，那么在样式表靠后的规则会胜出

## URL

在css中，url和开始的括号之间不可以有空格

```
body {background:url()}  正确
body {background:url ()} 错误
```

## image

```
<image>的src可以有三种值
url
image-set  只有少量浏览器支持
gradient 线性或径向渐变图像
```

## calc

css使用calc进行简单的数学计算

calc()会检查括号中各个值的类型，确保兼容，检查方式如下：

+和-两侧的值必须使用相同类型的值，或者是number，integer，结果为number，5+2.7是有效的5em+2.7是无效的，因为一边是长度单位，一边没有，5em+20px是有效的，因为都是长度单位

*计算的两个值中必须有一个是number，因此2.5rem * 2是有效的，2rem * 2.5rem是无效的，因为结果是5rem^2,这是面积单位不是长度单位

/计算的两个值右边必须是number，左边是integer时，值式number，否则单位和左边一样，30em/2.75是有效的，30/2.75em是无效的

任何情况下都不可以/0

## attr()

## 颜色

在css中的颜色表示

- 具名颜色
- RGB颜色 可以使用百分比或者整数表示 rbg(100%,100%,100%) rgb(255,255,255),百分比的取值范围是0-100%，整数的范围0-255，都不能超过这个值，否则就会被裁减
- RGBA颜色。会比RGB的基础上增加一个alpha，即red-green-blue-alpha。范围是0-1
- 十六进制的RGB   h1{color:#FFFFFF}. 如果16进制中每两个数值相同，可以省略一个，css允许使用简短语法，h1{color:#FFF;},为什么可以这么写，因为浏览器会把每个数字复制成两个，#F00 会变成#FF0000，#6FA变成#66FFAA，像#808080不能这么写
- 十六进制的RGBA。#663399AA可以写成#639A
  - HSL和HSLa颜色。CSS3新增的颜色表示方法，HSL是Hue 色相 ，Satuation 饱和度，Lightness明度的简称色相是角度值取值范围是0-360，饱和度0-100的百分数明度是0-100的百分数。色相分布在一个色相环上，随着角度的旋转得到的色谱上的不同的颜色，0代表红色，旋转到360又回回到红色，p.one {color:hsl(0,%,0%)}

**颜色关键字**

有两个特殊的关键字可以在任何时候允许使用颜色值的地方使用，transparent，currentColor

transparent表示完全透明的颜色，按照css color module的定义，他与rgba(0,0,0,0)相同，这个关键字不用于定义文本颜色，主要用于定义背景色的默认值，还可以定义只占空间而不可见边框的元素，定义渐变也常用

currentColor是指当前元素color属性计算得到的值：

```
.main {color:gray;border-color:currentColor;}
```

## 角度

deg。度数，完整的圆周是360度；

grad。百分度，完整的圆周是400百分度；

rad。 弧度，完整的圆周是2π

turn  圈数 一个完整的圆周是一圈，这个单位在旋转动画中最有用，

## 自定义属性

```
html{
	--base-color:#639;
	--highlight-color:#AEA;
}

h1 {color: var(--base-color);}
h2 {color: var(--highlight-color);}

自定义标识符使用--作为开头，并使用var进行调用，区分大小写，自定义属性还有范围

html{
--base-color:#639;
}
aside{
--base-color:#F60;
}

aside中的变量作用范围在aside元素之下，html则作用于html元素之下
```

## 文本属性

### text-indent 文本缩进

取值:length/percentage

初始值：0

适用于：块级元素

百分数：相对于所在块级元素的宽度

计算值：百分数如上，长度值得到绝对长度

继承性：是

动画性：是

text-indent可以把元素缩进到指定的长度，缩进的长度可以是负值，例如`text-indent:-9999px;`,常用语缩进段落的第一行

```
p {text-indent:2em;}
```

Text-indent可以用于任何块级元素，缩进将沿行内方向展开，text-indent不能用于行内元素，或者置换元素例如图像，如果图像在块级元素的第一行，他将随行中的其他文本一起后移。

如果想缩进行内元素的首行，可以通过内边距或者外边距来实现，

text-indent可以是负值，常用的用法是实现悬挂缩进，即首行相比元素的其他内容悬挂在一边

```
p {text-indent:-4em;}
```

在使用负值时，避免被浏览器放入边框吃掉，建议在外部加上padding或者magin

### text-align

取值：start｜end｜left｜right｜center｜jutify｜match-parent｜start end

初始值：css3中是start，在css2.1中是由用户代理指定

适用于：块级元素

计算值：指定值，match-parent除外

继承性：是

动画性：否

在css2.1中，对从左到右书写的语言，text-align的默认值是left，从右至左的默认值是right，在css3中，对于纵向书写的语言，left和right对应着起边和终边。

**两端对齐。justify**

两端对齐的文本，一行的两端都要和父元素的边界对齐，单词和字母之间的空白会调整，从而保证每一行的长度一致，两端对齐的文本要经过拉升，正好正满父元素左右边界之间的空间，至于如何拉升，是由用户代理来决定的，css规定，letter-spcing设置了长度值，用户代理不因该在增加和减少字符之间的间距。

**和父元素保持一致**

match-parent ，浏览器不支持这个值，他的基本作用被inherit覆盖，

### text-align-last

对齐最后一行

取值 auto｜start｜end｜left｜right｜center｜justify

初始值 auto

适用于 块级元素

计算值 指定值

继承性 是

动画性 否

如果段落只有一行，就是第一行也是最后一行，test-align-last的优先级会高于text-align

### line-height

指的是行的基线之间的距离，和font-size无关，line-height是控制行距，是除字体高度之外在文本行上方的额外空间，也就是说，line-height的值和字体之间的高度差就是行距

取值  number｜length｜percentage ｜normal

初始值 normal

适用于 所有元素

百分数 相当于font-size

计算值 长度和百分数，得到绝对值，否则，是指定值

继承性 是

动画性 是

对于块级元素而言，line-height定义元素中文本行基线之间的最小距离，定义的是最小距离，而不是固定值，基线之间的距离可能比line-height的值大,line-height不影响置换元素的布局，但是依旧会应用到置换元素上。

**行的构成**

文本行的每个元素构成一个内容去，其高度由字体的高度居定，随内容区出现的是一个inline box行内框 ，如果不考虑其他因素，他的高度和内容区的高度相同，line-height是影响内容区高度的因素之一。

元素的行距等于font-size-line-height，行距可能是负数，行距分为两半，分别是内容区的上半部分和下半部分，算上行距，就得到元素的行内距。例如font-size是14px line-height是18px 二者差4px，那么这就会被分为内容区的上半部分和下半部分，

确定一行内容的全部行内框之后，行框也就确定了，行框恰好包围最高那个行内框的顶部和最低那个行内框的底部

**line-height的值***

默认是normal时，行之间的距离是由用户代理来确定，不同的用户代理可能不同，不过一般是font-size的1.2倍，因此行高一般会比font-size大

**line-height的继承**

块级元素之间继承的line-height有点复杂，从父元素继承line-height值时，根据父元素的font-size进行计算，而不是根据子元素计算，

例如

```css
body{font-size:10px;}
div {line-height:1em;}//10px;
p {font-size:18px;}
```

Line-height使用纯数字的时候`line-height:1;`，纯数字就会应用到当前元素及其子元素上，元素的line-heigh将根据自身的font-size*1来计算

```
div{line-height:1.5}
p{font-size:18px}  //继承得到的line-height=18*1.5px
```

如果把line-height：inherit，元素的line-height将变成父元素line-height的计算值。

### vertical-align

Vertical-align只能用于置换元素如img，input等和行内元素，vertical-align不是继承属性

取值  baseline sub super top text-top middle bottom text-bottom 具体数值。百分比

初始值 baseline

适用于 行内元素和单元格

百分数。相对于元素的line-height的值

计算值 百分数和长度值，得到绝对长度，否则是指定的值

继承性 否

动画性 具体数值，百分比

备注。用于单元格上时，只能取baseline，top ，middle，bottom

**baseline**

强制元素的基线和父元素的基线对齐，正常情况下，一行中所有文本元素的底端都是对齐的

如果目标元素没有基线，像表单，图像，表单输入框，或者其他置换元素，元素底端和父元素的基线对齐

**sub**

sub把元素放在下标处，即元素的基线，对于置换元素来说就是底边线，低于父元素的基线，规范没有定义下沉🉐️距离，因此在不同🉐️1用户代理可能不同，super的·作用和sub相反，让元素的基线或者置换元素的底边线高于父元素的基线，高出的距离由用户代理来决定。

sub和super不会改变元素的font-size

**bottom**

bottom把元素所在的行内框的底边和行框的底边对齐。

**top**

top和bottom相反

**middle**

middle通常用于image上，middle把行内元素所在方框的中线与父元素基线向上偏移0.5ex处的线对齐，(1ex相当于父元素的font-size)

多数用户代理把1ex视作是半个em，因此middle把行内元素所在方框的中线与父元素基线向上偏移1/4em

**百分数**

百分数的效果是把元素的基线或者置换元素的底边相对于父元素的基线抬升或者下沉指定的量，指定的百分数是相对于元素自身而非父元素的line- height计算，



提升或者下沉文本不会和其他行重叠，，纵向对齐元素可能会改变行高，行框是包含最高的那个行内框的顶端和最低那个行内框的底端，这包括verticle-aligh的高度

### word-sapcing 单词间距

取值   具体数值 normal

默认值 normal

适用于所有元素

计算值 normal的绝对长度为0 否则为指定的具体的长度

继承性 是

动画性 是

### letter-spacing 字符间距

取值 具体数值 normal

初始值 normal

适用于 所有元素

计算值 长度得到绝对长度，否则是normal

继承性 是

动画性 是

### 对齐方式对间距的影响

word-spacing的宽度可能会受到text-align的影响，如果text-align为两端对齐，为了让文字沾满整行，可能会调整文字间的宽度，word-spacing可能被调整，letter-spacing如果是数值则没有影响，如果是noraml，可能会改变距离。word-spacing和letter-spacing继承的都是计算后的值，而不是换算系数。为了让字符之间的间距和font-size成比例，必须给元素自身设置letter-spacing

### text-transform 文本转换

取值 uppercase lowercase capitalize none

初始值 none

适用于 所有值

计算值 指定值

继承性 是

动画性 是

### text-decoration 文本装饰

取值 none ｜underline ｜overline ｜line-through ｜ blink

初始值 none

适用于 所有元素

计算值 指定值

继承性 否

动画性 否

在一个规则中可以有多个装饰结果，如果有让a标签既有上划线又有下划线

```
a:link a:visited {text-decoration:underline overline;}
```

如果是在同一个元素上使用多个text-decoration，注意特指度

text-decoration不能够被继承

text-decoration和vertical-align同时使用，还会发生奇怪的事情，例如

```
p{text-decoration:overline;font-size:12pt;}
sup {vertical-align:50%;font-size:12pt;}
```

p元素上有上划线，sup元素是p的子元素，所以会出现sup元素被上划线穿过的情况

### text-rendering

svg属性，支持他的用户代理不会把它视为一个css属性

### text-shadow 文本阴影

取值 none ｜数值｜数值 数值 数值？

初始值 none

适用于 所有元素

继承性 否

动画性 是

```
text-shadow：颜色 行横偏移距离 纵向偏移距离 可选 模糊半径
```

大量的阴影或者模糊半径的文本阴影会损耗性能，尤其是在低损耗和CPU性能有限的情况下，例如移动设备

### white-space

他会影响用户代理对文档中的空格，换行符，制表符的处理方式

取值 noraml nowrap  pre pre-wrap pre-line

适用于 全部元素，块级元素

计算值 指定值

继承性 否

动画性 否

| 值       | 空白 | 换行符 | 自动换行 |
| -------- | ---- | ------ | -------- |
| pre-line | 折叠 | 保留   | 允许     |
| normal   | 折叠 | 忽略   | 允许     |
| nowrap   | 折叠 | 保留   | 禁止     |
| pre      | 保留 | 保留   | 禁止     |
| pre-wrap | 保留 | 保留   | 允许     |

## 视觉格式化基础

### 元素框基础

默认情况下，content的background可以出现在padding内，margin始终是透明的，padding不能是负值，外边距可以

border的颜色由border-color来确定，如果没有设置，那么他的颜色和content的background颜色一致，如果border的线型有间隙，那么可以看到元素的背景，border不能为负数

#### 常规流动

就是从左到右，从上至下的顺序，以及传统的HTML文档采取的文本布局方式

#### 非置换元素

内容包含在文档中的元素，p是非置换元素，因为p元素中的文本内容是在元素自身中

#### 置换元素

为其他内容占位的元素，典型的置换元素是img，多数表单元素也是置换元素

##### 根元素

位于文档树顶端的元素，在html文档中，根元素是html，在xml文档中，根元素可以是语言允许的任何元素，例如，RSS文件的根元素是rss。

##### 块级框

段落，标签，或者div等元素生成的框，在常规流动模式下，块级框在框体前货都换行，因此块级框是纵向堆叠的，display：block可以把任何元素的框变成块级框

##### 行内框

strong，span生成的框，行内框前后不换行，display：inline声明能把任何元素生成的框体变成行内框

##### 行内块级框

内部特征像行内框，外部特征像块级框，行内块级框的行为和置换元素相似，但不完全相同

#### containing block

containing block是由离元素最近的那个生成列表项目或者块级框(包含所有与表格有关的框体，例如单元格生成的框体)的祖辈元素构成

```
<body>
	<div>
		<p>this is a paragraph</p>
	</div>
</body>
```

p元素的containing block是div，因为他是祖辈元素中离p元素最近的，div的containing block是body，p元素的布局依赖于div元素，div元素的布局依赖于body元素，html元素是body元素的containing block，html元素是 initial containning block，他的独特之处在于他的尺寸是由视区即浏览器的窗口决定的，而非根元素内容的尺寸

### 调整元素显示方式

#### 改变元素的显示方式

display可以改变显示方式，但是不会改变元素的特性，把p元素变成行内框，但是不会把p元素变成行内元素，行内元素可以作为块级元素的后代，反过来一般不行，

#### 块级框

<img src="https://raw.githubusercontent.com/yangzeng-cell/blogimage2/master/%E6%88%AA%E5%B1%8F2023-03-29%2022.29.45.png" style="zoom:50%;" />

默认情况下，块级框的宽度就是左内边界到右内边界的距离，就是conten的宽度，高度就是下内边界到上内边界之间的距离，就是content的高度

可以通过box-sizing来改变块级框的width和height的具体的意义

#### 横向格式化

```
width:160px;
padding:20px;
border:10px solid;
box-sizing：border-box;
content宽度=width-2*padding-border*2
```



```
width:160px;
padding:20px;
border:10px solid;
box-sizing：content-box;
content宽度=width
```

#### 横向格式化属性

Margin-left,padding-left,margin-right,padding-right,border-left,border-right,width

Width,margin-left,margin-right可以设置为auto，其余的值要么是具体的值，要么是默认值

#### 使用auto

width和margin-left，margin-right只有有一个值设置为auto，另外两个值设置为具体的值·，那么设置为auto的值的宽度就会自动适应父元素的宽度，例如没有设置padding，border的情况下，

```css
.parent {
        width: 500px;
        background-color: red;
        height: 500px;
      }

      .child {
        width: 100px;
        background-color: green;
        height: 100px;
        margin-right: auto;//margin-right会变成300px
        margin-left: 100px;
        /* box-sizing: border-box; */
        /* border: 10px solid black; */
      }
```

如果width，margin-right，margin-left都设置为100px

```css
 .parent {
        width: 500px;
        background-color: red;
        height: 500px;
      }

      .child {
        width: 100px;
        background-color: green;
        height: 100px;
        margin-right: 100px;////margin-right会重置为auto
        margin-left: 100px;
        /* box-sizing: border-box; */
        /* border: 10px solid black; */
      }
```



```css
 .parent {
        width: 500px;
        background-color: red;
        height: 500px;
      }

      .child {
        width: auto;//500-100-100 这种也可以放元素水平居中
        background-color: green;
        height: 100px;
        margin-right: 100px;
        margin-left: 100px;
        /* box-sizing: border-box; */
        /* border: 10px solid black; */
      }
```



```css
 .parent {
        width: 500px;
        background-color: red;
        height: 500px;
      }

      .child {
        width: 100px;
        background-color: green;
        height: 100px;
        margin-right: auto;//margin会平分剩余空间，可以让元素水平居中
        margin-left: auto;
        /* box-sizing: border-box; */
        /* border: 10px solid black; */
      }
```



```css
      .parent {
        width: 500px;
        background-color: red;
        height: 500px;
      }

      .child {
        width: auto;
        background-color: green;
        height: 100px;
        margin-right: 100px;
        margin-left: auto;//margin-left,width都设置为auto，这样会让margin-left变成0，width设置为剩余的宽度400px
        /* box-sizing: border-box; */
        /* border: 10px solid black; */
      }
```



```css
 .parent {
        width: 500px;
        background-color: red;
        height: 500px;
      }

      .child {
        width: auto;
        background-color: green;
        height: 100px;
        margin-right: auto;
        margin-left: auto;
        /* box-sizing: border-box; */
        /* border: 10px solid black; */
      }
/*三个都设置为auto，此时margin为默认值0，width为父元素的width*/
```

#### 负外边距

```css
.parent {
        width: 500px;
        background-color: red;
        height: 500px;
      }

      .child {
        width: auto;
        background-color: green;
        height: 100px;
        margin-right: -50px;
        margin-left: 10px;
      }
 /*width+margin-right+margin-left=500px于是width=540px*/
```

把margin设置为负值，子元素的width比父元素的width宽

```css
 .parent {
        width: 500px;
        background-color: red;
        height: 500px;
      }

      .child {
        width: auto;
        background-color: green;
        height: 600px;
        margin-right: auto;
        margin-left: 10px;
        border：3px solid；
      }
/*margin-right=500-600-10-6*/
```

只有margin可以设置为负值，当有一个只设置为auto时，就会让margin+padding+border+width=父元素的width

#### 置换元素

当置换元素的width为auto的时候，置换元素的width会等于内容自身的宽度。如果置换元素的宽度和自身的宽度不同，高度也会随之变化，如果设置了height，width为auto，width也会随高度变化

#### 纵向变化属性

height方向和width方向一致，都有7个值，如果把margin-top，margin-bottom设置为auto，则会为0

#### 折叠纵行外边距

在垂直方向上两个响铃块元素的margin会发生重叠，大的margin会覆盖小的margin。父子元素的margin也会发生重叠，

```
 .parent {
        margin-top: 50px;
        width: 500px;
        background-color: red;
        height: 500px;
      }

      .child {
        width: auto;
        background-color: green;
        height: 100px;
        margin-top: 100px;
      }
```

父元素和子元素都设置了margin-top，而且互相接触，如果子元素的margin-top大于父元素的margin-top，他们重叠之后的margin-top为子元素的margin-top，如果父元素的margin-top大于子元素，则会取父元素的margin-top，他们的上边框会重叠在一起。

#### 负外边距和重叠

```
li {margin-bottom: 20px;}
ul {margin-bottom: -15px;}
h1 {margin-top: -18px;}
```

两个负外边距中，绝对值更大的会覆盖绝对值更小的，在和正外边距相加，所以实际的margin为2px

### 行内元素

table元素及其子元素和块级元素和行内元素有很大的区别

```cpp
	span{
        border: 1px solid yellow;
        background-color: blue;
        text-align: right;
        word-wrap: break-word;
        white-space: normal;
      }
```

span在连续的数字和字母的情况下不会出现换行，需要使用` word-wrap: break-word; white-space: normal;`来强制让他换行

行距：font-size和line-heigh之差，二者之间的差值/2，添加到内容区的上下部分。只有非置换元素有行距。

行内框：内容区加上行距之后的边框，对于非置换元素来说，行内框的高度=line-height。

行框：各行内框最高点到最低点的距离，就是最高行内框的顶边到最低行内框的底边的距离

内容区相当于块级框的内容框

行内元素的banckground在内容区和内边距的区域内

非置换行内元素的内边距，边框，外边距在高度上没有效果

置换元素的外边距和边框对行内框的高度有影响

行内框的在一行中的纵向对齐方式由vertical-align来决定

一行中各元素的行内框的高度是这样确定的：

- 确定非置换元素和匿名文本的font-size和line-height的差/2加到文本的上下部分
- 确定各置换元素的heigh，padding-top，padding-bottom,margin-top,margin-bottom,border-top-width,border-bottom-width,相加
- 确定内容区在一行基线上方和下方超出多少，要把置换元素的底边与一行的基线对齐
- 确定vertical- aligne的值
- 知道所有行内框的位置之后，要计算行框的高度：基线和最高那个行内框之间的距离加上基线和最低行内框之间的距离

#### 行内格式化

所有元素都有line-height属性，无论是否有显式声明，line-height会影响行内元素及其内部，但不会影响块级元素，块级元素设置line-height是为块级元素的内容设置最小行框高度

#### 行内非置换元素

行框的构成：

对于非置换元素和匿名文本，font-size决定着内容区的高度，如果font-size：15px；那么内容区的高度为15px，line-height-font-size/2加到内容区的上下部分就可以得到行内框的高度，相减为负数也是一样操作

#### 纵向对齐

vertical-align：

top：元素行内框的顶边和所在行框的顶边对齐

bottom： 元素行内框的底边和所在行内框的底边对齐

text-top：元素行内框的顶边和父元素内容区的顶边对齐

text-bottom： 元素行内框的底边与父元素内容区的底边对齐

middle：元素行内框的纵向中点与父元素的基线向上偏移0.5ex处对齐

super：向上移动元素的内容区和行内框，无法制定移动距离

sub：向下移动元素的内容框和行内框

<percentage>  : 向上向下移动元素，移动的距离为line-height*百分比，line-height可能是继承自父元素



使用line-height可以控制上下行不会出现重叠。

**line-height是相对于元素本身的font-size来进行计算的**

line-height的最佳设定方式是使用比例因子，`line-height:1.5;`,因为line-height会被子元素继承，所以实际的line-height为font-size*比例因子。

padding，margin，border可以用于行内非置换元素，当时不会增加行框的高度，边框的边界是由font-size控制的。

#### 行内置换元素

行内置换元素例如image 有自己的height，width。置换元素的行内框包括content，padding，margin，border。

行内置换元素的line-height也是有值的，因为vertify-align需要使用到line-height。行内置换元素的padding在content四周，border会围绕padding，padding和border会影响行框的高度，因为他们是行内置换元素的一部分，margin也在行框中，正margin会增加行框的高度，负margin会减少行框的高度，并且可能和临近的上边元素重叠

行内置换元素默认是和基线对齐，由于行内替换元素没有基线，所以会让margin的下底边和基线对齐

#### 行内块级元素

`display:inline-block`就可以将元素设置为行内块级元素

行内块级元素与其他元素和内容的关系按照行内框来处理，也就是说，他在一行中的布局方式和image相同，行内块级元素实际上是当行内块级元素来格式化，意味着行内块级元素的底部默认和基线对齐。

行内块级元素中的content会当作块级元素来进行格式化，width，height，box-sizing可以用于行内块级元素

## padding，margin，border，outline

### width，height

width，height	默认值是auto，适用于行内非替换元素table元素中的tr，td之外的元素

### padding

适用于所有元素，background默认延伸到padding，可以使用background-clip来设置

padding的百分数是相对父元素的width来计算的

行内非替换元素的padding对行高没有影响，但是行内非替换元素设置了background，padding-top/bottom，padding会向上或者向下延伸，造成和前面的行重叠，如果设置了padding-left，padding-right，会向左右留出空白，如果显示多行的时候，只会在第一行的首部和最后一行的末尾有空白

### border

默认情况下，border的颜色和color的颜色一致

#### outline

outline不占据空间

outline可以不是矩形

用户代理经常在：focus时候渲染outline

outline对布局没有任何影响

#### margin

margin默认值为0，margin的百分数是相对于父元素内容区的宽度计算

相邻色上下margin会折叠，取最大值，父元素的margin和子元素的margin也会重叠，也是取最大值

行内非替换元素的margin始终是透明的，margin对行高没有影响，但是设置左右margin则会有间距出现，设置多行的margin又不同。

行内置换元素设置margin会影响行高

## color，background，linear-gradient

### 前景色

设置元素前景色的最简单的方法是color，前景色会默认应用到border上，如果没有设置border-color，那么默认颜色和color是一致的。

border使用前景色的行为来自一个特殊的关键字，currentColor，currentColor始终为color的计算值，设置border-color可以覆盖currentColor

表单元素也可以设置前景色，例如input，select，radio

color会被继承

### background

background-color可以设置背景色，任何元素都可以使用background-color，默认值是tranparent

#### background-clip

设置背景可以延伸到那个位置，border-box，padding-box，content-box，text，初始值是border-box，background-clip对根元素html，body没有效果，border-radis会影响background-clip的效果

#### background-image

background-image适用于所有元素

#### background-position

适用于块级元素和置换元素，初始值是0% 0%，

| 关键字       | 等效关键字    | 等效百分比 |
| ------------ | ------------- | ---------- |
| center       | center center | 50% 50%    |
| Right        | Center right  | 100% 50%   |
| Left         | Center left   | 0% 50%     |
| Top          | top center    | 50% 0      |
| Bottom       | Bottom center | 50% 100%   |
| Top left     | left top      | 0 0        |
| top right    | right top     | 100% 0%    |
| Bottom right | right bottom  | 100% 100%  |
| bottom left  | left bottom   | 0 100%     |

#### Background-rapeat

#### Background-attchement

Background-attchementk可以把background-image在固定的视图内，避免受滚动的影响。

默认是scroll，还有属性fixed，local

#### background-size

#### background

顺序上没要求

background-size必须紧跟background-position后面，并用/隔开

横值在前，纵值在后

如果同时设置background-origin，background-clip，前面的给background-origin，后面的给background-clip，如果只有一个与之相关的值，则是属于两个

设置多个background属性时，用逗号分隔，多个背景分布在不同的层，background-color不能设置多个值，多个background中最先设置的会在最上面，

### 渐变

有两种图像由css来实现的：线性渐变和径向渐变，二者有分为循环渐变和不循环渐变。渐变可以在任何可以使用image的地方。

渐变是指一个颜色向另外一个颜色平滑的过度，0%表示白色，100%表示黑色。**渐变是图像**，渐变没有尺寸，如果background没有声明banckground-size，默认值是auto，渐变值为100%

#### 线性渐变

```css
div {background-image:linear-gradient(purple,gold);}
div {background-image:linear-gradient(90edg,purple,gold);}
div {background-image:linear-gradient(to left,purple,gold);}
div {background-image:linear-gradient(-135deg,purple,gold);}
div {background-image:linear-gradient(to bottom left,purple,gold,navy);}
```

linear-gradient的color可以有多个

```
div{background-image:linear-gradient(90deg,red,orange 25px,yellow 50px,green 75px,blue 100px,indigo	125px,violet 15opx);}
```

可以设置每个color的长度，如果是用百分比，50%是梯度线的终点，两条相同的色标放在一起会重叠

#### 径向渐变

```
div{background-image:radial-gradient(purple,gold);}
```

### box-shadow

```
div {box-shadow:横向偏移 纵向偏移 模糊距离 颜色}
div {box-shadow:inset 横向偏移 纵向偏移 模糊距离 颜色} inset表示是在里面渲染是内凹的
```

一个元素可以有任意个box-shadow，并用逗号隔开

## float

float元素脱离常规文档流，元素浮动后，其他content会围绕他流动，浮动元素可以设置宽高。margin，padding，border，不管什么类型，浮动之后生成的是块级框，即使是行内框，浮动之后会变成块级框。

规则：

- 浮动元素的左右边界不能超过容纳块的左右边界

- 如果向左浮动，那么浮动块会从左向右排列，如果向右浮动则会从右向左排列，除非浮动块的宽度无法占据剩余的宽度，才会换行

- 左浮动元素的右外边界不能再右浮动元素左外边界的右侧，右浮动元素的左外边界不能再左浮动元素的右外边界

- 浮动元素的顶边不能比父元素的内顶边高，如果浮动元素位于两个折叠的外边距之间，在两个元素之间放置他的位置时，将视其有个块级元素

- 浮动元素的顶边不能比前方任何一个浮动元素或者块级元素的顶边高

- 浮动元素的顶边不能高于文档中出现在浮动元素之前的元素生成的框体所在行框的顶边

- 左浮动元素的左边如果还有一个向左浮动的元素，那么他的右外边界不能在容纳块右边界的右侧，类似的，右浮动元素的右边如果还有一个向右的浮动元素，那么他的右边界不能再容纳块的左边界的左侧

- 浮动元素必须放在尽可能高的位置上

- 左浮动元素必须尽量向左移动，右浮动元素必须尽量向右移动。位置越高，向左或者向右移动的距离越远

浮动元素的后代元素也是浮动时，将扩大范围，涵盖浮动的后代元素。

在有负外边距的情况下，浮动元素会会超出父元素

浮动元素比父元素宽时，会溢出

### 浮动元素和内容重叠

行内框和浮动元素重叠时，其border，background，content都会在浮动元素之上渲染

块级元素与浮动元素重叠时，border，background会在浮动元素背后渲染，content会在浮动元素之上渲染

### 清除浮动

```
clear：both｜left｜right｜none；
```

### shape-outside

浮动周围的元素按什么方式流动

none：除了浮动元素自己的margin，不产生任何影响

base-shape：

- inset() //将形状框内凹 inset(2.5em) 默认形状框是margin-box

- Circle()//圆形

- Ellipse()//椭圆形

- Polygon()//多边形

  

shade-box：

- Margin-box
- Border-box
- Padding-box
- Content-box

默认使用margin-box

base-shape和shade-box可以一起使用

```
shape-outside：inset(10) border-box;
```

### shape-image-threshold

**`shape-image-threshold`** 通过设定一个 alpha 通道的界限值来提取[`shape-outside`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/shape-outside) 值为图像的形状。

这个属性用于指定 透明度为多少时，允许内容流入

### shape-margin

[CSS](https://developer.mozilla.org/zh-CN/docs/Web/CSS) 属性 **`shape-margin`** 用于设定由[`shape-outside`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/shape-outside)创建的 CSS 形状的外边距。

## position

static｜relative ｜sticky｜absolute｜fixed

static：默认的类型，块级元素生成矩形框，位于文档流中，行内元素生成一个或者多个行框，随父元素流动

relative：元素框偏移一定的距离，元素所占的空间也是和正常情况下一样

absolute：脱离文档流，相对于设置了position非默认属性的父元素进行定位，否则就是相对于视口进行定位，无论在文档流中是什么元素，设置了absolute之后都会变成块级框

fixed：相对于视口进行定位

sticky：元素已开始是位于文档流中，当达到触发条件时，就会脱离文档流，不过会在文档流中占据空间，这个时候是相对于position：absolute进行定位的，触发条件失效后，会回到文档流中

#### min-width，min-height，max-width，max-height

适用于除了置换行内元素和表格元素之外的元素，百分比都是针对父元素的宽度和高度进行计算

#### overflow

visible，hidden，scroll，auto

初始值为visible，使用块级元素和置换元素

#### visibility

visible，hidden，collapse

初始值是visible

适用于所有元素

和display：none的1区别，display会让元素从文档流中删除，不再对文档流有影响，visibility：hidden是将元素应仓，但是还是存在于文档流中。设置为hidden的元素，其后代也是hidden，可以将后代元素设置为visible

visibility：collapse用于渲染表格

#### position：absolute；

```
span {
	position:absolute;
	top:auto;
	left:0;
}
```

这时候定位元素的顶部将和设置为position：static；的时候的顶部一致，即为脱离文档流时一致

```
span {
	position:absolute;
	top:auto;
	left:auto;
}
```

此时由于脱离文档流，会和文档流中的元素重叠

#### 非置换元素的尺寸和位置

$$
left+margin-left+margin-right+right+padding-right+padding-left=容纳块的width
$$

```
span {
	position:absolute;
	top:0;
	left:1em;
	right：1em；
	width：10em；
	margin-left：1em；
	margin-right：1em；
}//如果比父元素的宽度小，这时候right会被重置满足要求，这和right：auto是一致的。
```



```
span {
	position:absolute;
	top:0;
	left:1em;
	right：1em；
	width：10em；
	margin-left：1em；
	margin-right：auto；
}//如果比父元素的宽度小，margin-right会被匹配适当的值。
```



```
span {
	position:absolute;
	top:0;
	width：100%；
	bottom：0；
	margin：auto 0；
}//
元素剧中显示
```

在横向布局中，如果left或者right为auto，左右边界的位置会根据static时的确定，在纵向布局中，只有top才是根据static的位置确定，bottom任何时候都不这样确定。在纵向布局中，首先会判断top的值是否存在，如果top值不为auto，在确定满足容纳块高度的情况下，确定bottom的距离。纵向尺寸过约束，bottom会被忽略，但是top永远不会被忽略

#### 置换元素的位置和尺寸

- 置换元素会有自己原来的尺寸，如果width设置为auto，则使用自己原来的尺寸
- 在从左到右的语言中，left设置为auto，auto设置为static的位置，在从右至左的语言中，right设置为auto，auto会设置为static的位置
- 在right/left：auto，上一步auto没有替换时，margin-left/margin-right：0
- 如果这时候right/left还是auto，则把right。left设置为相同值，元素会居中
- 如果最后还有值为auto，则会设置为最后等于容纳块的值

和非置换元素一样，如果过约束，用户代理会忽略right/left的值

纵向布局也是一样

#### z-index

初始值为auto，适用于定位元素

无论元素的z-index的正负值，都显示在父元素的前面，不会在父元素的背后绘制，元素的z-index是相对于局部的堆叠上下文，即容纳块。

auto：生成的框体在当前堆叠上下文中的堆叠次序是0，如果不是跟元素，不确立新的堆叠上下文	，z-index：auto===z-index：0

flex布局和grid布局元素不使用position，但是也受z-index的控制

#### 固定定位

#### 相对定位

postion：relative只是会移动一定的距离，元素还是在原来的位置上

```
strong {position:relative;top:10px;bottom:20px};
```

relative过约束，会把其中一个值设置为绝对值的负数

#### 粘性定位

## flex布局

### flex-direction

取值：row｜row-reverse｜column｜column-reverse

初始值：row

用于定义主轴的方向

### flex-wrap

取值：nowrap｜wrap｜wrap-reverse

初始值：nowrap
