---
title: CSS权威指南
date: 2022-11-30 22:47:43
tags:
- [CSS权威指南]
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

### containing block

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
