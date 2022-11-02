---
title: scss笔记
date: 2022-10-30 23:35:55
tags:
- [scss]
categories:
- [CSS,scss]
---

### 变量

```scss
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}
```

scss的变量是以$开头的，css也有自己的变量，他和scss不同，scss变量会被scss编译，css变量会包含在输出文件中，css变量对不同的元素有不同的值，scss变量一次只有一个值，scss变量是命令式的，这意味着如果你使用一个变量然后改变它的值，之前使用的值不变，css是声明事的，如果中途改变了值，拿他之前和之后的值都会发生变化

```scss
$variable: value 1;
.rule-1 {
  value: $variable;
}

$variable: value 2;
.rule-2 {
  value: $variable;
}
```

css

```css
.rule-1 {
  value: value 1;
}

.rule-2 {
  value: value 2;
}
```

Sass 变量与所有 Sass 标识符一样，将连字符和下划线视为相同。这意味着`$font-size`and`$font_size`都指的是同一个变量。这是 Sass 早期的历史遗留问题，当时*它只*允许在标识符名称中使用下划线。一旦 Sass 添加了对连字符的支持以匹配CSS的语法，这两者就等同于使迁移更容易。

#### 默认值

!default scss提供了这个符号来编写默认变量，特别是当你编写scss库的时候

```scss
// _library.scss
$black: #000 !default;
$border-radius: 0.25rem !default;
$box-shadow: 0 0.5rem 1rem rgba($black, 0.15) !default;

code {
  border-radius: $border-radius;
  box-shadow: $box-shadow;
}
```

```scss
// style.scss
@use 'library' with (
  $black: #222,会覆盖默认的变量值
  $border-radius: 0.1rem
);
```

css

```scss
code {
  border-radius: 0.1rem;
  box-shadow: 0 0.5rem 1rem rgba(34, 34, 34, 0.15);
}
```

不能修改由[内置模块](https://sass-lang.com/documentation/modules)定义的变量

```scss
@use "sass:math" as math;

// This assignment will fail.
math.$pi: 0;
```

#### 范围

在样式表顶层声明的变量是*全局*的。这意味着在声明它们之后，可以在其模块中的任何地方访问它们。但并非所有变量都是如此。在块中声明的那些（ SCSS中的花括号或 Sass 中的缩进代码）通常是*local*，并且只能在它们声明的块中访问。

```scss
$global-variable: global value;

.content {
  $local-variable: local value;
  global: $global-variable;
  local: $local-variable;
}

.sidebar {
  global: $global-variable;

  // This would fail, because $local-variable isn't in scope:
  // local: $local-variable;
}
```

css

```scss
.content {
  global: global value;
  local: local value;
}

.sidebar {
  global: global value;
}
```

局部变量甚至可以声明为与全局变量同名。如果发生这种情况，实际上有两个不同的变量同名：一个是本地的，一个是全局的。这有助于确保编写局部变量的作者不会意外更改他们甚至不知道的全局变量的值。

```css
$variable: global value;

.content {
  $variable: local value;
  value: $variable;
}

.sidebar {
  value: $variable;
}
```

css

```css

.content {
  value: second global value;
}

.sidebar {
  value: second global value;
}
```

如果您需要在本地范围内（例如在 mixin 中）设置全局变量的值，则可以使用该`!global`标志。标记为的变量声明`!global`将*始终*分配给全局范围。

```scss
$variable: first global value;

.content {
  $variable: second global value !global;
  value: $variable;
}

.sidebar {
  value: $variable;
}
```

Css

```css
.content {
  value: second global value;
}

.sidebar {
  value: second global value;
}
```

#### 流程控制

```scss
$dark-theme: true !default;
$primary-color: #f8bbd0 !default;
$accent-color: #6a1b9a !default;

@if $dark-theme {
  $primary-color: darken($primary-color, 60%);
  $accent-color: lighten($accent-color, 60%);
}

.button {
  background-color: $primary-color;
  border: 1px solid $accent-color;
  border-radius: 3px;
}
```

css

```scss
.button {
  background-color: #750c30;
  border: 1px solid #f5ebfc;
  border-radius: 3px;
}
```

### ⚠️注意！

流控制范围内的变量可以修改外部范围内的现有变量，但它们不能在那里声明新变量。确保变量在分配之前已经声明，即使您需要将其声明为 `null`.

### 嵌套

```scss
nav {
  ul {
    margin: 0;
    padding: 0;
    list-style: none;
  }

  li { display: inline-block; }

  a {
    display: block;
    padding: 6px 12px;
    text-decoration: none;
  }
}
```

### scss片段

你可以创建一个scss片段进行重复利用，例如编写一个以下环线开头的scss文件，而这个文件不会被编写成css文件，他可以在其他scss中被引用，使用@use进行引用这个文件

### 模块

```scss
// _base.scss
$font-stack: Helvetica, sans-serif;
$primary-color: #333;

body {
  font: 100% $font-stack;
  color: $primary-color;
}
```



```scss
// styles.scss
@use 'base';

.inverse {
  background-color: base.$primary-color;
  color: white;
}
```

### 混入 

```scss
@mixin theme($theme: DarkGray) {
  background: $theme;
  box-shadow: 0 0 1px rgba($theme, .25);
  color: #fff;
}

.info {
  @include theme;
}
.alert {
  @include theme($theme: DarkRed);
}
.success {
  @include theme($theme: DarkGreen);
}

```

转化成

```css
.info {
  background: DarkGray;
  box-shadow: 0 0 1px rgba(169, 169, 169, 0.25);
  color: #fff;
}

.alert {
  background: DarkRed;
  box-shadow: 0 0 1px rgba(139, 0, 0, 0.25);
  color: #fff;
}

.success {
  background: DarkGreen;
  box-shadow: 0 0 1px rgba(0, 100, 0, 0.25);
  color: #fff;
}
```

### 继承

```scss
/* This CSS will print because %message-shared is extended. */
%message-shared {
  border: 1px solid #ccc;
  padding: 10px;
  color: #333;
}

// This CSS won't print because %equal-heights is never extended.
%equal-heights {
  display: flex;
  flex-wrap: wrap;
}

.message {
  @extend %message-shared;
}

.success {
  @extend %message-shared;
  border-color: green;
}

.error {
  @extend %message-shared;
  border-color: red;
}

.warning {
  @extend %message-shared;
  border-color: yellow;
}
```

### 操作数

```scss
@use "sass:math";

.container {
  display: flex;
}

article[role="main"] {
  width: math.div(600px, 960px) * 100%;
}

aside[role="complementary"] {
  width: math.div(300px, 960px) * 100%;
  margin-left: auto;
}
```

### 选择器列表

```scss
.alert, .warning {
  ul, p {
    margin-right: 0;
    margin-left: 0;
    padding-bottom: 0;
  }
}
```

转成css

```css
.alert ul, .alert p, .warning ul, .warning p {
  margin-right: 0;
  margin-left: 0;
  padding-bottom: 0;
}
```

### 选择器组合器

您也可以嵌套使用[组合](https://developer.mozilla.org/en-US/docs/Web/CSS/CSS_Selectors#Combinators#Combinators)器的选择器。您可以将组合器放在外部选择器的末尾，内部选择器的开头，或者甚至都放在两者之间。

```scss
ul > {
  li {
    list-style-type: none;
  }
}

h2 {
  + p {
    border-top: 1px solid gray;
  }
}

p {
  ~ {
    span {
      opacity: 0.8;
    }
  }
}
```

等价于css

```css
ul > li {
  list-style-type: none;
}

h2 + p {
  border-top: 1px solid gray;
}

p ~ span {
  opacity: 0.8;
}
```

### 插值

您可以使用[插值](https://sass-lang.com/documentation/interpolation)将 变量和函数调用等[表达式中的值注入选择器。](https://sass-lang.com/documentation/syntax/structure#expressions)这在您编写[mixins](https://sass-lang.com/documentation/at-rules/mixin)时特别有用，因为它允许您根据用户传入的参数创建选择器。

```scss
@mixin define-emoji($name, $glyph) {
  span.emoji-#{$name} {
    font-family: IconFont;
    font-variant: normal;
    font-weight: normal;
    content: $glyph;
  }
}

@include define-emoji("women-holding-hands", "👭");
```

转成css

```scss
@charset "UTF-8";
span.emoji-women-holding-hands {
  font-family: IconFont;
  font-variant: normal;
  font-weight: normal;
  content: "👭";
}
```

插值几乎可以在 Sass 样式表中的任何地方使用，以将[SassScript 表达式](https://sass-lang.com/documentation/syntax/structure#expressions)的结果嵌入到 CSS 块中。只需在以下任何地方包装一个表达式`#{}`：

#### 在sassscript中使用

```scss
@mixin inline-animation($duration) {
  $name: inline-#{unique-id()};

  @keyframes #{$name} {
    @content;
  }

  animation-name: $name;
  animation-duration: $duration;
  animation-iteration-count: infinite;
}

.pulse {
  @include inline-animation(2s) {
    from { background-color: yellow }
    to { background-color: red }
  }
}
```

转成css

```css
.pulse {
  animation-name: inline-uoqjh6kaq;
  animation-duration: 2s;
  animation-iteration-count: infinite;
}
@keyframes inline-uoqjh6kaq {
  from {
    background-color: yellow;
  }
  to {
    background-color: red;
  }
}

```

#### 引用字符串

```scss
.example {
  unquoted: #{"string"};
}
```

Css

```css
.example {
  unquoted: #{"string"};
}
```



### 属性声明

在sass中和在css中一样，属性声明定义匹配元素选择器的样式，但是sass添加了额外的功能，例如样式的值可以是任何变量

```scss
.circle {
  $size: 100px;
  width: $size;
  height: $size;
  border-radius: $size * 0.5;
}
```

转成css

```css
.circle {
  width: 100px;
  height: 100px;
  border-radius: 50px;
}
```

属性的名称可以包含[插值](https://sass-lang.com/documentation/interpolation)，这使得可以根据需要动态生成属性。您甚至可以插入整个属性名称！

```scss
@mixin prefix($property, $value, $prefixes) {
  @each $prefix in $prefixes {
    -#{$prefix}-#{$property}: $value;
  }
  #{$property}: $value;
}

.gray {
  @include prefix(filter, grayscale(50%), moz webkit);
}
```

转成

```css
.gray {
  -moz-filter: grayscale(50%);
  -webkit-filter: grayscale(50%);
  filter: grayscale(50%);
}
```

许多CSS属性都以作为一种命名空间的相同前缀开头。例如，`font-family`，`font-size`，并且`font-weight`都以 开头`font-`。Sass 通过允许嵌套属性声明使这变得更容易且更少冗余。外部属性名称被添加到内部，用连字符分隔。

```scss
.enlarge {
  font-size: 14px;
  transition: {
    property: font-size;
    duration: 4s;
    delay: 2s;
  }

  &:hover { font-size: 36px; }
}
```

css

```css
.enlarge {
  font-size: 14px;
  transition-property: font-size;
  transition-duration: 4s;
  transition-delay: 2s;
}
.enlarge:hover {
  font-size: 36px;
}

```

其中一些CSS属性具有使用命名空间作为属性名称的简写版本。对于这些，您可以编写简写值*和* 更明确的嵌套版本

```scss
.info-page {
  margin: auto {
    bottom: 10px;
    top: 2px;
  }
}
```

Css

```css
.info-page {
  margin: auto;
  margin-bottom: 10px;
  margin-top: 2px;
}
```

如果希望通过true/false来控制属性的隐藏和展示，如果一个声明的值是[`null`](https://sass-lang.com/documentation/values/null)或一个空的未加[引号的字符串](https://sass-lang.com/documentation/values/strings#unquoted)，Sass 根本不会将该声明编译成CSS。

```scss
$rounded-corners: false;

.button {
  border: 1px solid black;
  border-radius: if($rounded-corners, 5px, null);
}
```

css

```css
.button {
  border: 1px solid black;
}
```

定义css变量

```scss
$primary: #81899b;
$accent: #302e24;
$warn: #dfa612;

:root {
  --primary: #{$primary};
  --accent: #{$accent};
  --warn: #{$warn};

  // Even though this looks like a Sass variable, it's valid CSS so it's not
  // evaluated.
  --consumed-by-js: $primary;
}
```

css

```css
:root {
  --primary: #81899b;
  --accent: #302e24;
  --warn: #dfa612;
  --consumed-by-js: $primary;
}
```

### 父选择器

父选择器`&`是 Sass 发明的一种特殊选择器，在[嵌套选择器](https://sass-lang.com/documentation/style-rules#nesting)中用于引用外部选择器。它可以以更复杂的方式重用外部选择器，例如添加[伪类](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-classes)或在父级*之前添加选择器。*

当在内部选择器中使用父选择器时，它会被相应的外部选择器替换。发生这种情况而不是正常的嵌套行为

```scss
.alert {
  // The parent selector can be used to add pseudo-classes to the outer
  // selector.
  &:hover {
    font-weight: bold;
  }

  // It can also be used to style the outer selector in a certain context, such
  // as a body set to use a right-to-left language.
  [dir=rtl] & {
    margin-left: 0;
    margin-right: 10px;
  }

  // You can even use it as an argument to pseudo-class selectors.
  :not(&) {
    opacity: 0.8;
  }
}
```

css

```css
.alert:hover {
  font-weight: bold;
}
[dir=rtl] .alert {
  margin-left: 0;
  margin-right: 10px;
}
:not(.alert) {
  opacity: 0.8;
}
```

您还可以使用父选择器向外部选择器添加额外的后缀。[这在使用像BEM](http://getbem.com/) 这样使用高度结构化类名的方法时特别有用。只要外部选择器以字母数字名称结尾（如类、ID和元素选择器），您就可以使用父选择器附加附加文本。

```scss
.accordion {
  max-width: 600px;
  margin: 4rem auto;
  width: 90%;
  font-family: "Raleway", sans-serif;
  background: #f4f4f4;

  &__copy {
    display: none;
    padding: 1rem 1.5rem 2rem 1.5rem;
    color: gray;
    line-height: 1.6;
    font-size: 14px;
    font-weight: 500;

    &--open {
      display: block;
    }
  }
}
```

Css

```css
.accordion {
  max-width: 600px;
  margin: 4rem auto;
  width: 90%;
  font-family: "Raleway", sans-serif;
  background: #f4f4f4;
}
.accordion__copy {
  display: none;
  padding: 1rem 1.5rem 2rem 1.5rem;
  color: gray;
  line-height: 1.6;
  font-size: 14px;
  font-weight: 500;
}
.accordion__copy--open {
  display: block;
}
```

### 占位符选择器

占位符选择器是以%开头的选择器，他不会包含在输出css文件中，

```scss
.alert:hover, %strong-alert {
  font-weight: bold;
}

%strong-alert:hover {
  color: red;
}
```

css

```css
.alert:hover {
  font-weight: bold;
}
```

占位符的使用

```scss
%toolbelt {
  box-sizing: border-box;
  border-top: 1px rgba(#000, .12) solid;
  padding: 16px 0;
  width: 100%;

  &:hover { border: 2px rgba(#000, .5) solid; }
}

.action-buttons {
  @extend %toolbelt;
  color: #4285f4;
}

.reset-buttons {
  @extend %toolbelt;
  color: #cddc39;
}
```

css

```css
.action-buttons, .reset-buttons {
  box-sizing: border-box;
  border-top: 1px rgba(0, 0, 0, 0.12) solid;
  padding: 16px 0;
  width: 100%;
}
.action-buttons:hover, .reset-buttons:hover {
  border: 2px rgba(0, 0, 0, 0.5) solid;
}

.action-buttons {
  color: #4285f4;
}

.reset-buttons {
  color: #cddc39;
}
```

