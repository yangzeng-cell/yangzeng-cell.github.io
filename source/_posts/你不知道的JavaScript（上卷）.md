---
title: 你不知道的JavaScript（上卷）
date: 2022-12-25 23:01:17
tags:
- [你不知道的JavaScript]
categories:
- [javascript]
---

# 作用域和闭包

## 作用域是什么

在传统编译语言的流程中，程序中的一段源代码在执行之前会经历三个步骤，统称为“编 译

**分词/词法分析（Tokenizing/Lexing）**

这个过程会将由字符组成的字符串分解成（对编程语言来说）有意义的代码块，这些代 码块被称为词法单元（token）。例如，考虑程序 var a = 2;。这段程序通常会被分解成 为下面这些词法单元：var、a、=、2 、;。空格是否会被当作词法单元，取决于空格在 这门语言中是否具有意义。

分词（tokenizing）和词法分析（Lexing）之间的区别是非常微妙、晦涩的， 主要差异在于词法单元的识别是通过有状态还是无状态的方式进行的。简 单来说，如果词法单元生成器在判断 a 是一个独立的词法单元还是其他词法 单元的一部分时，调用的是有状态的解析规则，那么这个过程就被称为词法 分析。 

**解析/语法分析（Parsing）**

这个过程是将词法单元流（数组）转换成一个由元素逐级嵌套所组成的代表了程序语法 结构的树。这个树被称为“抽象语法树”（Abstract Syntax Tree，AST）。 var a = 2; 的抽象语法树中可能会有一个叫作 VariableDeclaration 的顶级节点，接下 来是一个叫作 Identifier（它的值是 a）的子节点，以及一个叫作 AssignmentExpression 的子节点。AssignmentExpression 节点有一个叫作 NumericLiteral（它的值是 2）的子 节点。 

**代码生成**

将 AST 转换为可执行代码的过程称被称为代码生成。这个过程与语言、目标平台等息 息相关。抛开具体细节，简单来说就是有某种方法可以将 var a = 2; 的 AST 转化为一组机器指 令，用来创建一个叫作 a 的变量（包括分配内存等），并将一个值储存在 a 中。

比起那些编译过程只有三个步骤的语言的编译器，JavaScript 引擎要复杂得多。例如，在 语法分析和代码生成阶段有特定的步骤来对运行性能进行优化，包括对冗余元素进行优化 等。

首先，JavaScript 引擎不会有大量的（像其他语言编译器那么多的）时间用来进行优化，因 为与其他语言不同，JavaScript 的编译过程不是发生在构建之前的。 

对于 JavaScript 来说，大部分情况下编译发生在代码执行前的几微秒（甚至更短！）的时 间内。在我们所要讨论的作用域背后，JavaScript 引擎用尽了各种办法（比如 JIT，可以延 迟编译甚至实施重编译）来保证性能最佳。

简单地说，任何 JavaScript 代码片段在执行前都要进行编译（通常就在执行前）。因此， JavaScript 编译器首先会对 var a = 2; 这段程序进行编译，然后做好执行它的准备，并且 通常马上就会执行它。 

参与到对程序 var a = 2; 进行处理的过程中:

**引擎**

从头到尾负责整个 JavaScript 程序的编译及执行过程。

**编译器**

引擎的好朋友之一，负责语法分析及代码生成等脏活累活

**作用域**

引擎的另一位好朋友，负责收集并维护由所有声明的标识符（变量）组成的一系列查 询，并实施一套非常严格的规则，确定当前执行的代码对这些标识符的访问权限。

当你看见 var a = 2; 这段程序时，很可能认为这是一句声明。但引擎却不这 么看。事实上，引擎认为这里有两个完全不同的声明，一个由编译器在编译时处理，个则由引擎在运行时处理。

下面我们将 var a = 2; 分解，看看引擎和它的朋友们是如何协同工作的。

编译器会进行如下处理：

1.遇到 var a，编译器会询问作用域是否已经有一个该名称的变量存在于同一个作用域的 集合中。如果是，编译器会忽略该声明，继续进行编译；否则它会要求作用域在当前作 用域的集合中声明一个新的变量，并命名为 a。 

2.接下来编译器会为引擎生成运行时所需的代码，这些代码被用来处理 a = 2 这个赋值 操作。引擎运行时会首先询问作用域，在当前的作用域集合中是否存在一个叫作 a 的 变量。如果是，引擎就会使用这个变量；如果否，引擎会继续查找该变量

如果引擎最终找到了 a 变量，就会将 2 赋值给它。否则引擎就会举手示意并抛出一个异 常！

当变量出现在赋值操作的左侧时进行 LHS 查询，出现在右侧时进行 RHS 查询。RHS 查询与简单地查找某个变量的值别无二致，而 LHS 查询则是试图 找到变量的容器本身，从而可以对其赋值。从这个角度说，RHS 并不是真正意义上的“赋 值操作的右侧”，更准确地说是“非左侧”。 你可以将 RHS 理解成 retrieve his source value（取到它的源值），这意味着“得到某某的 值”。

考虑以下代码： 

```js
console.log( a ); 
```

其中对 a 的引用是一个 RHS 引用，因为这里 a 并没有赋予任何值。相应地，需要查找并取 得 a 的值，这样才能将值传递给 console.log(..)。 

相比之下，例如： 

```js
a = 2; 
```

这里对 a 的引用则是 LHS 引用，因为实际上我们并不关心当前的值是什么，只是想要为 = 2 这个赋值操作找到一个目标。

LHS 和 RHS 的含义是“赋值操作的左侧或右侧”并不一定意味着就是“= 赋值操作符的左侧或右侧”。赋值操作还有其他几种形式

考虑下面的程序，其中既有 LHS 也有 RHS 引用：

```js
function** foo(a) { 

console.log( a ); // 2 

}

foo( 2 ); 
```

最后一行 foo(..) 函数的调用需要对 foo 进行 RHS 引用，意味着“去找到 foo 的值，并把 它给我”。并且 (..) 意味着 foo 的值需要被执行，因此它最好真的是一个函数类型的值！ 

这里还有一个容易被忽略却非常重要的细节。 代码中隐式的 a＝2 操作可能很容易被你忽略掉。这个操作发生在 2 被当作参数传递给 foo(..) 函数时，2 会被分配给参数 a。为了给参数 a（隐式地）分配值，需要进行一次 LHS 查询

这里还有对 a 进行的 RHS 引用，并且将得到的值传给了 console.log(..)。console. log(..) 本身也需要一个引用才能执行，因此会对 console 对象进行 RHS 查询，并且检查 得到的值中是否有一个叫作 log 的方法。

最后，在概念上可以理解为在 LHS 和 RHS 之间通过对值 2 进行交互来将其传递进 log(..) （通过变量 a 的 RHS 查询）。假设在 log(..) 函数的原生实现中它可以接受参数，在将 2 赋 值给其中第一个（也许叫作 arg1）参数之前，这个参数需要进行 LHS 引用查询

你可能会倾向于将函数声明 function foo(a) {... 概念化为普通的变量声明 和赋值，比如 var foo、foo ＝ function(a) {...。如果这样理解的话，这 个函数声明将需要进行 LHS 查询。 然而还有一个重要的细微差别，编译器可以在代码生成的同时处理声明和值 的定义，比如在引擎执行代码时，并不会有线程专门用来将一个函数值“分 配给”foo。因此，将函数声明理解成前面讨论的 LHS 查询和赋值的形式并 不合适。 

## 作用域嵌套

当一个块或函数嵌套在另一个块或函数中时，就发生了作用域的嵌套。因此，在当前作用 域中无法找到某个变量时，引擎就会在外层嵌套的作用域中继续查找，直到找到该变量， 或抵达最外层的作用域（也就是全局作用域）为止。

## 异常 

为什么区分 LHS 和 RHS 是一件重要的事情？ 

因为在变量还没有声明（在任何作用域中都无法找到该变量）的情况下，这两种查询的行 为是不一样的。

```js
function foo(a){
console.log( a + b ); b = a; 
}
foo( 2 );
```

第一次对 b 进行 RHS 查询时是无法找到该变量的。也就是说，这是一个“未声明”的变 量，因为在任何相关的作用域中都无法找到它。 如果 RHS 查询在所有嵌套的作用域中遍寻不到所需的变量，引擎就会抛出 ReferenceError 异常。值得注意的是，ReferenceError 是非常重要的异常类型。

相较之下，当引擎执行 LHS 查询时，如果在顶层（全局作用域）中也无法找到目标变量， 全局作用域中就会创建一个具有该名称的变量，并将其返还给引擎，前提是程序运行在非 “严格模式”下。

ES5 中引入了“严格模式”。同正常模式，或者说宽松 / 懒惰模式相比，严格模式在行为上 有很多不同。其中一个不同的行为是严格模式禁止自动或隐式地创建全局变量。因此，在 严格模式中 LHS 查询失败时，并不会创建并返回一个全局变量，引擎会抛出同 RHS 查询 失败时类似的 ReferenceError 异常。 

接下来，如果 RHS 查询找到了一个变量，但是你尝试对这个变量的值进行不合理的操作， 比如试图对一个非函数类型的值进行函数调用，或着引用 null 或 undefined 类型的值中的 属性，那么引擎会抛出另外一种类型的异常，叫作 TypeError

ReferenceError 同作用域判别失败相关，而 TypeError 则代表作用域判别成功了，但是对 结果的操作是非法或不合理的

## 小结

作用域是一套规则，用于确定在何处以及如何查找变量（标识符）。如果查找的目的是对 变量进行赋值，那么就会使用 LHS 查询；如果目的是获取变量的值，就会使用 RHS 查询。赋值操作符会导致 LHS 查询。＝操作符或调用函数时传入参数的操作都会导致关联作用域 的赋值操作。

LHS 和 RHS 查询都会在当前执行作用域中开始，如果有需要（也就是说它们没有找到所 需的标识符），就会向上级作用域继续查找目标标识符，这样每次上升一级作用域（一层 楼），最后抵达全局作用域（顶层），无论找到或没找到都将停止。 

不成功的 RHS 引用会导致抛出 ReferenceError 异常。不成功的 LHS 引用会导致自动隐式 地创建一个全局变量（非严格模式下），该变量使用 LHS 引用的目标作为标识符，或者抛 出 ReferenceError 异常（严格模式下）。

# 词法作用域 

作用域共有两种主要的工作模型。第一种是最为普遍的，被大多数编程语言所采用的词法 作用域，我们会对这种作用域进行深入讨论。另外一种叫作动态作用域，仍有一些编程语 言在使用（比如 Bash 脚本、Perl 中的一些模式等）。

## 词法阶段

简单地说，词法作用域就是定义在词法阶段的作用域。换句话说，词法作用域是由你在写 代码时将变量和块作用域写在哪里来决定的，因此当词法分析器处理代码时会保持作用域 不变（大部分情况下是这样的）

作用域查找会在找到第一个匹配的标识符时停止。在多层的嵌套作用域中可以定义同名的 标识符，这叫作“遮蔽效应”（内部的标识符“遮蔽”了外部的标识符）。抛开遮蔽效应， 作用域查找始终从运行时所处的最内部作用域开始，逐级向外或者说向上进行，直到遇见 第一个匹配的标识符为止

全局变量会自动成为全局对象（比如浏览器中的 window 对象）的属性，因此 可以不直接通过全局对象的词法名称，而是间接地通过对全局对象属性的引 用来对其进行访问。`window.a`通过这种技术可以访问那些被同名变量所遮蔽的全局变量。但非全局的变量 如果被遮蔽了，无论如何都无法被访问到。无论函数在哪里被调用，也无论它如何被调用，它的词法作用域都只由函数被声明时所处 的位置决定。 

词法作用域查找只会查找一级标识符，比如 a、b 和 c。如果代码中引用了 foo.bar.baz， 词法作用域查找只会试图查找 foo 标识符，找到这个变量后，对象属性访问规则会分别接 管对 bar 和 baz 属性的访问。 

## 欺骗词法

欺骗词法作用域会导致性能 下降

### eval

JavaScript 中的 eval(..) 函数可以接受一个字符串为参数，并将其中的内容视为好像在书 写时就存在于程序中这个位置的代码。换句话说，可以在你写的代码中用程序生成代码并 运行，就好像代码是写在那个位置的一样

在执行 eval(..) 之后的代码时，引擎并不“知道”或“在意”前面的代码是以动态形式插 入进来，并对词法作用域的环境进行修改的。引擎只会如往常地进行词法作用域查找。

```
function foo(str, a) {
eval( str ); // 欺骗！ console.log( a, b );
}
var b = 2; 
foo( "var b = 3;", 1 ); // 1, 3
```

eval(..) 调用中的 "var b = 3;" 这段代码会被当作本来就在那里一样来处理。由于那段代 码声明了一个新的变量 b，因此它对已经存在的 foo(..) 的词法作用域进行了修改。事实 上，和前面提到的原理一样，这段代码实际上在 foo(..) 内部创建了一个变量 b，并遮蔽 了外部（全局）作用域中的同名变量。 当 console.log(..) 被执行时，会在 foo(..) 的内部同时找到 a 和 b，但是永远也无法找到 外部的 b。因此会输出“1, 3”而不是正常情况下会输出的“1, 2

默认情况下，如果 eval(..) 中所执行的代码包含有一个或多个声明（无论是变量还是函 数），就会对 eval(..) 所处的词法作用域进行修改eval在严格模式中无法修改他所在的词法作用域

```js
function foo(str) {
  "use strict";
  eval(str);
  console.log(a); // ReferenceError: a is not defined
}
foo("var a = 2");
```

JavaScript 中 还 有 其 他 一 些 功 能 效 果 和 eval(..) 很 相 似。setTimeout(..) 和 setInterval(..) 的第一个参数可以是字符串，字符串的内容可以被解释为一段动态生成的 函数代码。这些功能已经过时且并不被提倡。不要使用它们！

new Function(..) 函数的行为也很类似，最后一个参数可以接受代码字符串，并将其转 化为动态生成的函数（前面的参数是这个新生成的函数的形参）。这种构建函数的语法比 eval(..) 略微安全一些，但也要尽量避免使用。 

### with

with 通常被当作重复引用同一个对象中的多个属性的快捷方式，可以不需要重复引用对象 本身。

```js
var obj = { a: 1, b: 2, c: 3 }; // 单调乏味的重复 "obj"
obj.a = 2;
obj.b = 3;
obj.c = 4; // 简单的快捷方式
with (obj) {
  a = 3;
  b = 4;
  c = 5;
}
```



```js
function foo(obj) {
  with (obj) {
    a = 2;
  }
}
var o1 = { a: 3 };
var o2 = { b: 3 };
foo(o1);
console.log(o1.a); // 2 foo( o2 );
console.log(o2.a); // undefined
console.log(a); // 2——不好，a 被泄漏到全局作用域上了！
```

这个例子中创建了 o1 和 o2 两个对象。其中一个具有 a 属性，另外一个没有。foo(..) 函 数接受一个 obj 参数，该参数是一个对象引用，并对这个对象引用执行了 with(obj) {..}。 在 with 块内部，我们写的代码看起来只是对变量 a 进行简单的词法引用，实际上就是一个 LHS 引用，并将 2 赋值给它。 

当我们将 o1 传递进去，a＝2 赋值操作找到了 o1.a 并将 2 赋值给它，这在后面的 console. log(o1.a) 中可以体现。而当 o2 传递进去，o2 并没有 a 属性，因此不会创建这个属性， o2.a 保持 undefined

但是可以注意到一个奇怪的副作用，实际上 a = 2 赋值操作创建了一个全局的变量 a。这 是怎么回事？

with 可以将一个没有或有多个属性的对象处理为一个完全隔离的词法作用域，因此这个对 象的属性也会被处理为定义在这个作用域中的词法标识符。

尽管 with 块可以将一个对象处理为词法作用域，但是这个块内部正常的 var 声明并不会被限制在这个块的作用域中，而是被添加到 with 所处的函数作 用域中。

eval(..) 函数如果接受了含有一个或多个声明的代码，就会修改其所处的词法作用域，而 with 声明实际上是根据你传递给它的对象凭空创建了一个全新的词法作用域。

可以这样理解，当我们传递 o1 给 with 时，with 所声明的作用域是 o1，而这个作用域中含 有一个同 o1.a 属性相符的标识符。但当我们将 o2 作为作用域时，其中并没有 a 标识符， 因此进行了正常的 LHS 标识符查找

o2 的作用域、foo(..) 的作用域和全局作用域中都没有找到标识符 a，因此当 a＝2 执行 时，自动创建了一个全局变量（因为是非严格模式）。 

另外一个不推荐使用 eval(..) 和 with 的原因是会被严格模式所影响（限 制）。with 被完全禁止，而在保留核心功能的前提下，间接或非安全地使用 eval(..) 也被禁止了。

### 性能

eval(..) 和 with 会在运行时修改或创建新的作用域，以此来欺骗其他在书写时定义的词 法作用域。

JavaScript 引擎会在编译阶段进行数项的性能优化。其中有些优化依赖于能够根据代码的 词法进行静态分析，并预先确定所有变量和函数的定义位置，才能在执行过程中快速找到 标识符。

但如果引擎在代码中发现了 eval(..) 或 with，它只能简单地假设关于标识符位置的判断 都是无效的，因为无法在词法分析阶段明确知道 eval(..) 会接收到什么代码，这些代码会 如何对作用域进行修改，也无法知道传递给 with 用来创建新词法作用域的对象的内容到底 是什么。 

最悲观的情况是如果出现了 eval(..) 或 with，所有的优化可能都是无意义的，因此最简 单的做法就是完全不做任何优化。 

如果代码中大量使用 eval(..) 或 with，那么运行起来一定会变得非常慢。无论引擎多聪 明，试图将这些悲观情况的副作用限制在最小范围内，也无法避免如果没有这些优化，代 码会运行得更慢这个事实。

## 小结

词法作用域意味着作用域是由书写代码时函数声明的位置来决定的。编译的词法分析阶段 基本能够知道全部标识符在哪里以及是如何声明的，从而能够预测在执行过程中如何对它 们进行查找

JavaScript 中有两个机制可以“欺骗”词法作用域：eval(..) 和 with。前者可以对一段包 含一个或多个声明的“代码”字符串进行演算，并借此来修改已经存在的词法作用域（在 运行时）。后者本质上是通过将一个对象的引用当作作用域来处理，将对象的属性当作作 用域中的标识符来处理，从而创建了一个新的词法作用域（同样是在运行时）

这两个机制的副作用是引擎无法在编译时对作用域查找进行优化，因为引擎只能谨慎地认 为这样的优化是无效的。使用这其中任何一个机制都将导致代码运行变慢。不要使用它们。

# 函数作用域和块作用域

## 函数中的作用域

函数作用域的含义是指，属于这个函数的全部变量都可以在整个函数的范围内使用及复 用（事实上在嵌套的作用域中也可以使用）。这种设计方案是非常有用的，能充分利用 JavaScript 变量可以根据需要改变值类型的“动态”特性。

## 隐藏内部实现

对函数的传统认知就是先声明一个函数，然后再向里面添加代码。但反过来想也可以带来 一些启示：从所写的代码中挑选出一个任意的片段，然后用函数声明对它进行包装，实际 上就是把这些代码“隐藏”起来了。

实际的结果就是在这个代码片段的周围创建了一个作用域气泡，也就是说这段代码中的任 何声明（变量或函数）都将绑定在这个新创建的包装函数的作用域中，而不是先前所在的 作用域中。换句话说，可以把变量和函数包裹在一个函数的作用域中，然后用这个作用域 来“隐藏”它们。

为什么“隐藏”变量和函数是一个有用的技术？

有很多原因促成了这种基于作用域的隐藏方法。它们大都是从最小特权原则中引申出来 的，也叫最小授权或最小暴露原则。这个原则是指在软件设计中，应该最小限度地暴露必 要内容，而将其他内容都“隐藏”起来，比如某个模块或对象的 API 设计

这个原则可以延伸到如何选择作用域来包含变量和函数。如果所有变量和函数都在全局作 用域中，当然可以在所有的内部嵌套作用域中访问到它们。但这样会破坏前面提到的最小 特权原则，因为可能会暴漏过多的变量或函数，而这些变量或函数本应该是私有的，正确 的代码应该是可以阻止对这些变量或函数进行访问的。

```js
function doSomething(a) {
  b = a + doSomethingElse(a * 2);
  console.log(b * 3);
}
function doSomethingElse(a) {
  return a - 1;
}
var b;
doSomething(2); // 15
```

在这个代码片段中，变量 b 和函数 doSomethingElse(..) 应该是 doSomething(..) 内部具体 实现的“私有”内容。给予外部作用域对 b 和 doSomethingElse(..) 的“访问权限”不仅 没有必要，而且可能是“危险”的，因为它们可能被有意或无意地以非预期的方式使用， 从而导致超出了 doSomething(..) 的适用条件。更“合理”的设计会将这些私有的具体内 容隐藏在 doSomething(..) 内部，，例如：

```js
unction doSomething(a) {
  function doSomethingElse(a) {
    return a - 1;
  }
  var b;
  b = a + doSomethingElse(a * 2);
  console.log(b * 3);
}
doSomething(2); // 15
```

现在，b 和 doSomethingElse(..) 都无法从外部被访问，而只能被 doSomething(..) 所控制。 功能性和最终效果都没有受影响，但是设计上将具体内容私有化了，设计良好的软件都会 依此进行实现。

“隐藏”作用域中的变量和函数所带来的另一个好处，是可以避免同名标识符之间的冲突， 两个标识符可能具有相同的名字但用途却不一样，无意间可能造成命名冲突。冲突会导致 变量的值被意外覆盖。

```js
function foo() {
  function bar(a) {
    i = 3; // 修改 for 循环所属作用域中的 i console.log( a + i );
  }
  for (var i = 0; i < 10; i++) {
    bar(i * 2); // 糟糕，无限循环了！
  }
}
foo();
```

bar(..) 内部的赋值表达式 i = 3 意外地覆盖了声明在 foo(..) 内部 for 循环中的 i。在这 个例子中将会导致无限循环，因为 i 被固定设置为 3，永远满足小于 10 这个条件

bar(..) 内部的赋值操作需要声明一个本地变量来使用，采用任何名字都可以，var i = 3; 就可以满足这个需求（同时会为 i 声明一个前面提到过的“遮蔽变量”）。另外一种方法是 采用一个完全不同的标识符名称，比如 var j = 3;。但是软件设计在某种情况下可能自然 而然地要求使用同样的标识符名称，因此在这种情况下使用作用域来“隐藏”内部声明是 唯一的最佳选择。

#### 全局命名空间

变量冲突的一个典型例子存在于全局作用域中。当程序中加载了多个第三方库时，如果它 们没有妥善地将内部私有的函数或变量隐藏起来，就会很容易引发冲突。

这些库通常会在全局作用域中声明一个名字足够独特的变量，通常是一个对象。这个对象 被用作库的命名空间，所有需要暴露给外界的功能都会成为这个对象（命名空间）的属 性，而不是将自己的标识符暴漏在顶级的词法作用域中

```js
var MyReallyCoolLibrary = {
  awesome: "stuff",
  doSomething: function () {
    // ...
  },
  doAnotherThing: function () {
    // ...
  },
};
```

#### 模块管理

另外一种避免冲突的办法和现代的模块机制很接近，就是从众多模块管理器中挑选一个来 使用。使用这些工具，任何库都无需将标识符加入到全局作用域中，而是通过依赖管理器 的机制将库的标识符显式地导入到另外一个特定的作用域中。

显而易见，这些工具并没有能够违反词法作用域规则的“神奇”功能。它们只是利用作用 域的规则强制所有标识符都不能注入到共享作用域中，而是保持在私有、无冲突的作用域 中，这样可以有效规避掉所有的意外冲突。

因此，只要你愿意，即使不使用任何依赖管理工具也可以实现相同的功效

## 函数作用域

我们已经知道，在任意代码片段外部添加包装函数，可以将内部的变量和函数定义“隐 藏”起来，外部作用域无法访问包装函数内部的任何内容。

```js
var a = 2;
function foo() {
  // <-- 添加这一行
  var a = 3;
  console.log(a); // 3
} // <-- 以及这一行
foo(); // <-- 以及这一行
console.log(a); // 2

```

虽然这种技术可以解决一些问题，但是它并不理想，因为会导致一些额外的问题。首先， 必须声明一个具名函数 foo()，意味着 foo 这个名称本身“污染”了所在作用域（在这个 例子中是全局作用域）。其次，必须显式地通过函数名（foo()）调用这个函数才能运行其 中的代码。

如果函数不需要函数名（或者至少函数名可以不污染所在作用域），并且能够自动运行， 这将会更加理想。

幸好，JavaScript 提供了能够同时解决这两个问题的方案、

```js
var a = 2;

(function foo() {
  // <-- 添加这一行
  var a = 3;
  console.log(a); // 3
})(); // <-- 以及这一行
console.log(a); // 2
```

首先，包装函数的声明以 (function... 而不仅是以 function... 开始。尽管看上去这并不 是一个很显眼的细节，但实际上却是非常重要的区别。函数会被当作函数表达式而不是一 个标准的函数声明来处理。 

区分函数声明和表达式最简单的方法是看 function 关键字出现在声明中的位 置（不仅仅是一行代码，而是整个声明中的位置）。如果 function 是声明中 的第一个词，那么就是一个函数声明，否则就是一个函数表达式。

函数声明和函数表达式之间最重要的区别是它们的名称标识符将会绑定在何处。

比较一下前面两个代码片段。第一个片段中 foo 被绑定在所在作用域中，可以直接通过 foo() 来调用它。第二个片段中 foo 被绑定在函数表达式自身的函数中而不是所在作用域中。

换句话说，(function foo(){ .. }) 作为函数表达式意味着 foo 只能在 .. 所代表的位置中 被访问，外部作用域则不行。foo 变量名被隐藏在自身中意味着不会非必要地污染外部作 用域

#### 匿名和具名

对于函数表达式你最熟悉的场景可能就是回调参数了，比如：

```js
setTimeout(function () {
  console.log("I waited 1 second!");
}, 1000);
```

这叫作匿名函数表达式，因为 function().. 没有名称标识符。函数表达式可以是匿名的， 而函数声明则不可以省略函数名——在 JavaScript 的语法中这是非法的。

匿名函数表达式书写起来简单快捷，很多库和工具也倾向鼓励使用这种风格的代码。但是 它也有几个缺点需要考虑。

匿名函数在栈追踪中不会显示出有意义的函数名，使得调试很困难。

如果没有函数名，当函数需要引用自身时只能使用已经过期的 arguments.callee 引用， 比如在递归中。另一个函数需要引用自身的例子，是在事件触发后事件监听器需要解绑 自身。

匿名函数省略了对于代码可读性 / 可理解性很重要的函数名。一个描述性的名称可以让 代码不言自明。 

行内函数表达式非常强大且有用——匿名和具名之间的区别并不会对这点有任何影响。给函 数表达式指定一个函数名可以有效解决以上问题。始终给函数表达式命名是一个最佳实践：

```js
etTimeout(function timeoutHandler() {
  // <-- 快看，我有名字了！
  console.log("I waited 1 second!");
}, 1000);
```

#### 立即执行函数表达式

```js
var a = 2;
(function foo() {
  var a = 3;
  console.log(a); // 3
})();
console.log(a); // 2
```

由于函数被包含在一对 ( ) 括号内部，因此成为了一个表达式，通过在末尾加上另外一个 ( ) 可以立即执行这个函数，比如 (function foo(){ .. })()。第一个 ( ) 将函数变成表达式，第二个 ( ) 执行了这个函数。

这种模式很常见，几年前社区给它规定了一个术语：IIFE，代表立即执行函数表达式 （Immediately Invoked Function Expression）； 

函数名对 IIFE 当然不是必须的，IIFE 最常见的用法是使用一个匿名函数表达式。虽然使 用具名函数的 IIFE 并不常见，但它具有上述匿名函数表达式的所有优势，因此也是一个值 得推广的实践。

```js
var a = 2;
(function IIFE() {
  var a = 3;
  console.log(a); // 3
})();
console.log(a); // 2
```

相较于传统的 IIFE 形式，很多人都更喜欢另一个改进的形式：(function(){ .. }())。仔 细观察其中的区别。第一种形式中函数表达式被包含在 ( ) 中，然后在后面用另一个 () 括 号来调用。第二种形式中用来调用的 () 括号被移进了用来包装的 ( ) 括号中。

IIFE 的另一个非常普遍的进阶用法是把它们当作函数调用并传递参数进去。

```js
var a = 2;
(function IIFE(global) {
  var a = 3;
  console.log(a); // 3
  console.log(global.a); // 2
})(window);
console.log(a); // 2
```

这个模式的另外一个应用场景是解决 undefined 标识符的默认值被错误覆盖导致的异常（虽 然不常见）。将一个参数命名为 undefined，但是在对应的位置不传入任何值，这样就可以 保证在代码块中 undefined 标识符的值真的是 undefined

```js
undefined = true; // 给其他代码挖了一个大坑！绝对不要这样做！
(function IIFE(undefined) {
  var a;
  if (a === undefined) {
    console.log("Undefined is safe here!");
  }
})();

```

IIFE 还有一种变化的用途是倒置代码的运行顺序，将需要运行的函数放在第二位，在 IIFE 执行之后当作参数传递进去。这种模式在 UMD（Universal Module Definition）项目中被广 泛使用。尽管这种模式略显冗长，但有些人认为它更易理解。

```js
var a = 2;
(function IIFE(def) {
  def(window);
})(function def(global) {
  var a = 3;
  console.log(a); // 3
  console.log(global.a); // 2
});

```

函数表达式 def 定义在片段的第二部分，然后当作参数（这个参数也叫作 def）被传递进 IIFE 函数定义的第一部分中。最后，参数 def（也就是传递进去的函数）被调用，并将 window 传入当作 global 参数的值

## 块作用域

```js
for (var i = 0; i < 10; i++) {
  console.log(i);
}
```

我们在 for 循环的头部直接定义了变量 i，通常是因为只想在 for 循环内部的上下文中使 用 i，而忽略了 i 会被绑定在外部作用域（函数或全局）中的事实。

这就是块作用域的用处。变量的声明应该距离使用的地方越近越好，并最大限度地本地 化。另外一个例子：

```js
var foo = true;
if (foo) {
  var bar = foo * 2;
  bar = something(bar);
  console.log(bar);
}
```

bar 变量仅在 if 声明的上下文中使用，因此如果能将它声明在 if 块内部中会是一个很有 意义的事情。但是，当使用 var 声明变量时，它写在哪里都是一样的，因为它们最终都会属于外部作用域。这段代码是为了风格更易读而伪装出的形式上的块作用域，如果使用这 种形式，要确保没在作用域其他地方意外地使用 bar 只能依靠自觉性。

#### with 

With也是块级作用域

#### try/catch

非常少有人会注意到 JavaScript 的 ES3 规范中规定 try/catch 的 catch 分句会创建一个块作 用域，其中声明的变量仅在 catch 内部有效。

```js

try {
  undefined(); // 执行一个非法操作来强制制造一个异常
} catch (err) {
  console.log(err); // 能够正常执行！
}
console.log(err); // ReferenceError: err not found
```

正如你所看到的，err 仅存在 catch 分句内部，当试图从别处引用它时会抛出错误尽管这个行为已经被标准化，并且被大部分的标准 JavaScript 环境（除了老 版本的 IE 浏览器）所支持，但是当同一个作用域中的两个或多个 catch 分句 用同样的标识符名称声明错误变量时，很多静态检查工具还是会发出警告。 实际上这并不是重复定义，因为所有变量都被安全地限制在块作用域内部， 但是静态检查工具还是会很烦人地发出警告。 为了避免这个不必要的警告，很多开发者会将 catch 的参数命名为 err1、 err2 等。也有开发者干脆关闭了静态检查工具对重复变量名的检查。

#### let

let 关键字可以将变量绑定到所在的任意作用域中（通常是 { .. } 内部）。换句话说，let 为其声明的变量隐式地了所在的块作用域。

```js
if (foo) {
  let bar = foo * 2;

  bar = something(bar);

  console.log(bar);
}

console.log(bar); // ReferenceError
```

用 let 将变量附加在一个已经存在的块作用域上的行为是隐式的。在开发和修改代码的过 程中，如果没有密切关注哪些块作用域中有绑定的变量，并且习惯性地移动这些块或者将 其包含在其他的块中，就会导致代码变得混乱。

为块作用域显式地创建块可以部分解决这个问题，使变量的附属关系变得更加清晰。通常 来讲，显式的代码优于隐式或一些精巧但不清晰的代码。显式的块作用域风格非常容易书 写，并且和其他语言中块作用域的工作原理一致：

只要声明是有效的，在声明中的任意位置都可以使用 { .. } 括号来为 let 创建一个用于绑 定的块。在这个例子中，我们在 if 声明内部显式地创建了一个块，如果需要对其进行重 构，整个块都可以被方便地移动而不会对外部 if 声明的位置和语义产生任何影响。

使用 let 进行的声明不会在块作用域中进行提升

##### 垃圾收集

另一个块作用域非常有用的原因和闭包及回收内存垃圾的回收机制相关

```js
function process(data) {
  // 在这里做点有趣的事情
}
var someReallyBigData = {};
process(someReallyBigData);
var btn = document.getElementById("my_button");
btn.addEventListener(
  "click",
  function click(evt) {
    console.log("button clicked");
  },
  /*capturingPhase=*/ false
);
```

click 函数的点击回调并不需要 someReallyBigData 变量。理论上这意味着当 process(..) 执 行后，在内存中占用大量空间的数据结构就可以被垃圾回收了。但是，由于 click 函数形成 了一个覆盖整个作用域的闭包，JavaScript 引擎极有可能依然保存着这个结构（取决于具体 实现）。

块作用域可以打消这种顾虑，可以让引擎清楚地知道没有必要继续保存 someReallyBigData 了

```js

function process(data) {
  // 在这里做点有趣的事情
} // 在这个块中定义的内容可以销毁了！
{
  let someReallyBigData = {};
  process(someReallyBigData);
}
var btn = document.getElementById("my_button");
btn.addEventListener(
  "click",
  function click(evt) {
    console.log("button clicked");
  },
  /*capturingPhase=*/ false
);
```

##### let循环

```js
for (let i=0; i<10; i++) { 

console.log( i ); 

}

console.log( i ); // ReferenceError 
```

for 循环头部的 let 不仅将 i 绑定到了 for 循环的块中，事实上它将其重新绑定到了循环 的每一个迭代中，确保使用上一个循环迭代结束时的值重新进行赋值。

下面通过另一种方式来说明每次迭代时进行重新绑定的行为： 

```js
{
  let j;
  for (j = 0; j < 10; j++) {
    let i = j; // 每个迭代重新绑定！
    console.log(i);
  }
}
```

由于 let 声明附属于一个新的作用域而不是当前的函数作用域（也不属于全局作用域）， 当代码中存在对于函数作用域中 var 声明的隐式依赖时，就会有很多隐藏的陷阱，如果用 let 来替代 var 则需要在代码重构的过程中付出额外的精力

#### const

除了 let 以外，ES6 还引入了 const，同样可以用来创建块作用域变量，但其值是固定的 （常量）。之后任何试图修改值的操作都会引起错误

```js
var foo = true;
if (foo) {
  var a = 2;
  const b = 3; // 包含在 if 中的块作用域常量
  a = 3; // 正常 !
  b = 4; // 错误 !
}
console.log(a); // 3
console.log(b); // Ref

```

# 提升

包括变量和函数在内的所有声明都会在任何代码被执行前首先 被处理

```js
a = 2;
var a;
console.log(a);//2

console.log(a);//undefined
var a = 2;

```

当你看到 var a = 2; 时，可能会认为这是一个声明。但 JavaScript 实际上会将其看成两个 声明：var a; 和 a = 2;。第一个定义声明是在编译阶段进行的。第二个赋值声明会被留在 原地等待执行阶段。

只有声明本身会被提升，而赋值或其他运行逻辑会留在原地。如果提升改变 了代码执行的顺序，会造成非常严重的破坏。

```js
foo();
function foo() {
  console.log(a); // undefined
  var a = 2;
}
```

foo 函数的声明（这个例子还包括实际函数的隐含值）被提升了，因此第一行中的调用可 以正常执行。

函数声明会被提升，但是函数表达式却不会被提升

```js
foo(); // 不是 ReferenceError, 而是 TypeError!
var foo = function bar() {
  // ...
};

```

这段程序中的变量标识符 foo() 被提升并分配给所在作用域（在这里是全局作用域），因此 foo() 不会导致 ReferenceError。但是 foo 此时并没有赋值（如果它是一个函数声明而不 是函数表达式，那么就会赋值）。foo() 由于对 undefined 值进行函数调用而导致非法操作， 因此抛出 TypeError 异常。

同时也要记住，即使是具名的函数表达式，名称标识符在赋值之前也无法在所在作用域中使用：

```js
foo(); // TypeError bar(); // ReferenceError
var foo = function bar() {
  // ...
};
```

### 函数优先

函数声明和变量声明都会被提升。

但是一个值得注意的细节（这个细节可以出现在有多个 “重复”声明的代码中）是函数会首先被提升，然后才是变量

函数是一等公民

```js
foo(); // 1
var foo;
function foo() {
  console.log(1);
}
foo = function () {
  console.log(2);
};
```

注意，var foo 尽管出现在 function foo()... 的声明之前，但它是重复的声明（因此被忽 略了），因为函数声明会被提升到普通变量之前。

尽管重复的 var 声明会被忽略掉，但出现在后面的函数声明还是可以覆盖前面的。

```js

foo(); // 3
function foo() {
  console.log(1);
}
var foo = function () {
  console.log(2);
};
function foo() {
  console.log(3);
}

```

虽然这些听起来都是些无用的学院理论，但是它说明了在同一个作用域中进行重复定义是 非常糟糕的，而且经常会导致各种奇怪的问题。

一个普通块内部的函数声明通常会被提升到所在作用域的顶部，这个过程不会像下面的代 码暗示的那样可以被条件判断所控制： 

```js
foo(); // "b"
var a = true;
if (a) {
  function foo() {
    console.log("a");
  }
} else {
  function foo() {
    console.log("b");
  }
}
```

# 作用域闭包

```js

function foo() {
  var a = 2;
  function bar() {
    console.log(a);
  }
  return bar;
}
var baz = foo();
baz(); // 2
```

函数 bar() 的词法作用域能够访问 foo() 的内部作用域。然后我们将 bar() 函数本身当作 一个值类型进行传递。在这个例子中，我们将 bar 所引用的函数对象本身当作返回值

在 foo() 执行后，其返回值（也就是内部的 bar() 函数）赋值给变量 baz 并调用 baz()，实 际上只是通过不同的标识符引用调用了内部的函数 bar()

bar() 显然可以被正常执行。但是在这个例子中，它在自己定义的词法作用域以外的地方 执行。

在 foo() 执行后，通常会期待 foo() 的整个内部作用域都被销毁，因为我们知道引擎有垃 圾回收器用来释放不再使用的内存空间。由于看上去 foo() 的内容不会再被使用，所以很 自然地会考虑对其进行回收。

而闭包的“神奇”之处正是可以阻止这件事情的发生。事实上内部作用域依然存在，因此 没有被回收。谁在使用这个内部作用域？原来是 bar() 本身在使用。

拜 bar() 所声明的位置所赐，它拥有涵盖 foo() 内部作用域的闭包，使得该作用域能够一 直存活，以供 bar() 在之后任何时间进行引用。

bar() 依然持有对该作用域的引用，而这个引用就叫作闭包。

因此，在几微秒之后变量 baz 被实际调用（调用内部函数 bar），不出意料它可以访问定义时的词法作用域，因此它也可以如预期般访问变量 a。

这个函数在定义时的词法作用域以外的地方被调用。闭包使得函数可以继续访问定义时的 词法作用域。

当然，无论使用何种方式对函数类型的值进行传递，当函数在别处被调用时都可以观察到 闭包。

```js
function foo() {
  var a = 2;
  function baz() {
    console.log(a); // 2
  }
  bar(baz);
}
function bar(fn) {
  fn(); // 妈妈快看呀，这就是闭包！
}
```

把内部函数 baz 传递给 bar，当调用这个内部函数时（现在叫作 fn），它涵盖的 foo() 内部 作用域的闭包就可以观察到了，因为它能够访问 a。

传递函数当然也可以是间接的。

```js
var fn;
function foo() {
  var a = 2;
  function baz() {
    console.log(a);
  }
  fn = baz; // 将 baz 分配给全局变量
}
function bar() {
  fn(); // 妈妈快看呀，这就是闭包！
}
foo();
bar(); // 2
```

无论通过何种手段将内部函数传递到所在的词法作用域以外，它都会持有对原始定义作用 域的引用，无论在何处执行这个函数都会使用闭包

```js
function wait(message) {
  setTimeout(function timer() {
    console.log(message);
  }, 1000);
}
wait("Hello, closure!");
```

将一个内部函数（名为 timer）传递给 setTimeout(..)。timer 具有涵盖 wait(..) 作用域的闭包，因此还保有对变量 message 的引用。

wait(..) 执行 1000 毫秒后，它的内部作用域并不会消失，timer 函数依然保有 wait(..) 作用域的闭包。 

深入到引擎的内部原理中，内置的工具函数 setTimeout(..) 持有对一个参数的引用，这个 参数也许叫作 fn 或者 func，或者其他类似的名字。引擎会调用这个函数，在例子中就是 内部的 timer 函数，而词法作用域在这个过程中保持完整。这就是闭包。

本质上无论何时何地，如果将函数（访问它们各自的词法作用域）当作第一 级的值类型并到处传递，你就会看到闭包在这些函数中的应用。在定时器、事件监听器、 Ajax 请求、跨窗口通信、Web Workers 或者任何其他的异步（或者同步）任务中，只要使 用了回调函数，实际上就是在使用闭包！

```js
ar a = 2;
(function IIFE() {
  console.log(a);
})();

```

虽然这段代码可以正常工作，但严格来讲它并不是闭包。为什么？因为函数（示例代码中 的 IIFE）并不是在它本身的词法作用域以外执行的。它在定义时所在的作用域中执行（而 外部作用域，也就是全局作用域也持有 a）。a 是通过普通的词法作用域查找而非闭包被发现的。

尽管技术上来讲，闭包是发生在定义时的，但并不非常明显，就好像六祖慧能所说：“既 非风动，亦非幡动，仁者心动耳。”。 

尽管 IIFE 本身并不是观察闭包的恰当例子，但它的确创建了闭包，并且也是最常用来创建 可以被封闭起来的闭包的工具。因此 IIFE 的确同闭包息息相关，即使本身并不会真的使用 闭包。 

```js
for (var i = 1; i <= 5; i++) {
  setTimeout(function timer() {
    console.log(i);
  }, i * 1000);
}
```

正常情况下，我们对这段代码行为的预期是分别输出数字 1~5，每秒一次，每次一个。 但实际上，这段代码在运行时会以每秒一次的频率输出五次 6。

这是为什么？

首先解释 6 是从哪里来的。这个循环的终止条件是 i 不再 <=5。条件首次成立时 i 的值是 6。因此，输出显示的是循环结束时 i 的最终值。

仔细想一下，这好像又是显而易见的，延迟函数的回调会在循环结束时才执行。事实上， 当定时器运行时即使每个迭代中执行的是 setTimeout(.., 0)，所有的回调函数依然是在循 环结束后才会被执行，因此会每次输出一个 6 出来。

这里引伸出一个更深入的问题，代码中到底有什么缺陷导致它的行为同语义所暗示的不一 致呢？缺陷是我们试图假设循环中的每个迭代在运行时都会给自己“捕获”一个 i 的副本。但是 根据作用域的工作原理，实际情况是尽管循环中的五个函数是在各个迭代中分别定义的， 但是它们都被封闭在一个共享的全局作用域中，因此实际上只有一个 i。 

这样说的话，当然所有函数共享一个 i 的引用。循环结构让我们误以为背后还有更复杂的 机制在起作用，但实际上没有。如果将延迟函数的回调重复定义五次，完全不使用循环， 那它同这段代码是完全等价的。

下面回到正题。缺陷是什么？我们需要更多的闭包作用域，特别是在循环的过程中每个迭 代都需要一个闭包作用域。

IIFE 会通过声明并立即执行一个函数来创建作用域

```js
for (var i = 1; i <= 5; i++) {
  (function () {
    setTimeout(function timer() {
      console.log(i);
    }, i * 1000);
  })();
}

```

这样能行吗？试试吧，我等着你。

我不卖关子了。这样不行。但是为什么呢？我们现在显然拥有更多的词法作用域了。的确 每个延迟函数都会将 IIFE 在每次迭代中创建的作用域封闭起来。 

如果作用域是空的，那么仅仅将它们进行封闭是不够的。仔细看一下，我们的 IIFE 只是一 个什么都没有的空作用域。它需要包含一点实质内容才能为我们所用。

它需要有自己的变量，用来在每个迭代中储存 i 的值：

```js
for (var i = 1; i <= 5; i++) {
  (function () {
    var j = i;
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000);
  })();
}

```

可以对这段代码进行一些改进：

```js
for (var i = 1; i <= 5; i++) {
  (function (j) {
    setTimeout(function timer() {
      console.log(j);
    }, j * 1000);
  })(i);
}
```

当然，这些 IIFE 也不过就是函数，因此我们可以将 i 传递进去，如果愿意的话可以将变量 名定为 j，当然也可以还叫作 i。无论如何这段代码现在可以工作了。

在迭代内使用 IIFE 会为每个迭代都生成一个新的作用域，使得延迟函数的回调可以将新的 作用域封闭在每个迭代内部，每个迭代中都会含有一个具有正确值的变量供我们访问。

仔细思考我们对前面的解决方案的分析。我们使用 IIFE 在每次迭代时都创建一个新的作用 域。换句话说，每次迭代我们都需要一个块作用域。第 3 章介绍了 let 声明，可以用来劫 持块作用域，并且在这个块作用域中声明一个变量。

本质上这是将一个块转换成一个可以被关闭的作用域

```js
for (var i = 1; i <= 5; i++) {
  let j = i; // 是的，闭包的块作用域！
  setTimeout(function timer() {
    console.log(j);
  }, j * 1000);
}
```



for 循环头部的 let 声明还会有一 个特殊的行为。这个行为指出变量在循环过程中不止被声明一次，每次迭代都会声明。随 后的每个迭代都会使用上一个迭代结束时的值来初始化这个变量

## 模块

```js
function foo() {
  var something = "cool";
  var another = [1, 2, 3];
  function doSomething() {
    console.log(something);
  }
  function doAnother() {
    console.log(another.join(" ! "));
  }
}

```

正如在这段代码中所看到的，这里并没有明显的闭包，只有两个私有数据变量 something 和 another，以及 doSomething() 和 doAnother() 两个内部函数，它们的词法作用域（而这 就是闭包）也就是 foo() 的内部作用域。

```js
function CoolModule() {
  var something = "cool";
  var another = [1, 2, 3];
  function doSomething() {
    console.log(something);
  }
  function doAnother() {
    console.log(another.join(" ! "));
  }
  return { doSomething: doSomething, doAnother: doAnother };
}
var foo = CoolModule();
foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```

这个模式在 JavaScript 中被称为模块。最常见的实现模块模式的方法通常被称为模块暴露， 这里展示的是其变体

首先，CoolModule() 只是一个函数，必须要通过调用它来创建一个模块实例。如果不执行 外部函数，内部作用域和闭包都无法被创建。

其次，CoolModule() 返回一个用对象字面量语法 { key: value, ... } 来表示的对象。这 个返回的对象中含有对内部函数而不是内部数据变量的引用。我们保持内部数据变量是隐 藏且私有的状态。可以将这个对象类型的返回值看作本质上是模块的公共 API

这个对象类型的返回值最终被赋值给外部的变量 foo，然后就可以通过它来访问 API 中的 属性方法，比如 foo.doSomething()。

从模块中返回一个实际的对象并不是必须的，也可以直接返回一个内部函 数。jQuery 就是一个很好的例子。jQuery 和 $ 标识符就是 jQuery 模块的公 共 API，但它们本身都是函数（由于函数也是对象，它们本身也可以拥有属 性）。

doSomething() 和 doAnother() 函数具有涵盖模块实例内部作用域的闭包（通过调用 CoolModule() 实现）。当通过返回一个含有属性引用的对象的方式来将函数传递到词法作 用域外部时，我们已经创造了可以观察和实践闭包的条件。 

模块模式需要具备两个必要条件。

必须有外部的封闭函数，该函数必须至少被调用一次（每次调用都会创建一个新的模块实例）

封闭函数必须返回至少一个内部函数，这样内部函数才能在私有作用域中形成闭包，并且可以访问或者修改私有的状态。

一个具有函数属性的对象本身并不是真正的模块。从方便观察的角度看，一个从函数调用 所返回的，只有数据属性而没有闭包函数的对象并不是真正的模块。

上一个示例代码中有一个叫作 CoolModule() 的独立的模块创建器，可以被调用任意多次， 每次调用都会创建一个新的模块实例。当只需要一个实例时，可以对这个模式进行简单的 改进来实现单例模式：

```js
var foo = (function CoolModule() {
  var something = "cool";
  var another = [1, 2, 3];
  function doSomething() {
    console.log(something);
  }
  function doAnother() {
    console.log(another.join(" ! "));
  }
  return { doSomething: doSomething, doAnother: doAnother };
})();
foo.doSomething(); // cool
foo.doAnother(); // 1 ! 2 ! 3
```

我们将模块函数转换成了 IIFE（参见第 3 章），立即调用这个函数并将返回值直接赋值给 

单例的模块实例标识符 foo。 

模块也是普通的函数，因此可以接受参数：

```js
function CoolModule(id) {
  function identify() {
    console.log(id);
  }
  return { identify: identify };
}
var foo1 = CoolModule("foo 1");
var foo2 = CoolModule("foo 2");
foo1.identify(); // "foo 1"
foo2.identify(); // "foo 2"
```

模块模式另一个简单但强大的变化用法是，命名将要作为公共 API 返回的对象：

```js
var foo = (function CoolModule(id) {
  function change() {
    // 修改公共 API
    publicAPI.identify = identify2;
  }
  function identify1() {
    console.log(id);
  }
  function identify2() {
    console.log(id.toUpperCase());
  }
  var publicAPI = { change: change, identify: identify1 };
  return publicAPI;
})("foo module");
foo.identify(); // foo module
foo.change();
foo.identify(); // FOO MODULE
```

通过在模块实例的内部保留对公共 API 对象的内部引用，可以从内部对模块实例进行修改，包括添加或删除方法和属性，以及修改它们的值。 

#### 现代的模块机制

大多数模块依赖加载器 / 管理器本质上都是将这种模块定义封装进一个友好的 API。这里 并不会研究某个具体的库，为了宏观了解我会简单地介绍一些核心概念：

```js
var MyModules = (function Manager() {
  var modules = {};
  function define(name, deps, impl) {
    for (var i = 0; i < deps.length; i++) {
      deps[i] = modules[deps[i]];
    }
    modules[name] = impl.apply(impl, deps);
  }
  function get(name) {
    return modules[name];
  }
  return { define: define, get: get };
})();
```

这段代码的核心是 modules[name] = impl.apply(impl, deps)。为了模块的定义引入了包装 函数（可以传入任何依赖），并且将返回值，也就是模块的 API，储存在一个根据名字来管 理的模块列表中。

下面展示了如何使用它来定义模块： 

```js
MyModules.define("bar", [], function () {
  function hello(who) {
    return "Let me introduce: " + who;
  }
  return { hello: hello };
});
MyModules.define("foo", ["bar"], function (bar) {
  var hungry = "hippo";
  function awesome() {
    console.log(bar.hello(hungry).toUpperCase());
  }
  return { awesome: awesome };
});
var bar = MyModules.get("bar");
var foo = MyModules.get("foo");
console.log(bar.hello("hippo")); // Let me introduce: hippo
foo.awesome(); // LET ME INTRODUCE: HIPPO

```

## 小结     

当函数可以记住并访问所在的词法作用域，即使函数是在当前词法作用域之外执行，这时 就产生了闭包。

模块有两个主要特征：

（1）为创建内部作用域而调用了一个包装函数；（2）包装函数的返回 值必须至少包括一个对内部函数的引用，这样就会创建涵盖整个包装函数内部作用域的闭 包。

# 块作用域的替代方案

```js
{
  let a = 2;
  console.log(a); // 2
}
console.log(a); // ReferenceError
```

这段代码在 ES6 环境中可以正常工作。但是在 ES6 之前的环境中如何才能实现这个效果？ 答案是使用 catch

```js
try {
  throw 2;
} catch (a) {
  console.log(a); // 2
}
console.log(a); // ReferenceError

```

catch 分句具有块作用域，因此它可以在 ES6 之前的环境中作为块作用域的替代 方案

# **this**词法

```js
var obj = {
  id: "awesome",
  cool: function coolFn() {
    console.log(this.id);
  },
};
var id = "not awesome";
obj.cool(); // 酷
setTimeout(obj.cool, 100); // 不酷

```

问题在于 cool() 函数丢失了同 this 之间的绑定。解决这个问题有好几种办法，但最长用 的就是

 var self = this方式

```js
var obj = {
  count: 0,
  cool: function coolFn() {
    var self = this;
    if (self.count < 1) {
      setTimeout(function timer() {
        self.count++;
        console.log("awesome?");
      }, 100);
    }
  },
};
obj.cool(); // 酷吧？
```

this词法

```js
var obj = {
  count: 0,
  cool: function coolFn() {
    if (this.count < 1) {
      setTimeout(() => {
        // 箭头函数是什么鬼东西？
        this.count++;
        console.log("awesome?");
      }, 100);
    }
  },
};
obj.cool(); // 很酷吧 ?
```

bind绑定

```js
var obj = {
  count: 0,
  cool: function coolFn() {
    if (this.count < 1) {
      setTimeout(
        function timer() {
          this.count++; // this 是安全的 // 因为 bind(..)
          console.log("more awesome");
        }.bind(this),
        100
      ); // look, bind()!
    }
  },
};
obj.cool(); // 更酷了
```

# 关于**this**

this 关键字是 JavaScript 中最复杂的机制之一

## 为什么要用this

```js
function identify() {
  return this.name.toUpperCase();
}
function speak() {
  var greeting = "Hello, I'm " + identify.call(this);
  console.log(greeting);
}
var me = { name: "Kyle" };
var you = { name: "Reader" };
identify.call(me); // KYLE
identify.call(you); // READER
speak.call(me); // Hello, 我是 KYLE
speak.call(you); // Hello, 我是 READER
```

这段代码可以在不同的上下文对象（me 和 you）中重复使用函数 identify() 和 speak()， 不用针对每个对象编写不同版本的函数。

如果不使用 this，那就需要给 identify() 和 speak() 显式传入一个上下文对象

```js
function identify(context) {
  return context.name.toUpperCase();
}
function speak(context) {
  var greeting = "Hello, I'm " + identify(context);
  console.log(greeting);
}
identify(you); // READER s
peak(me); //hello, 我是 KYLE

```

然而，this 提供了一种更优雅的方式来隐式“传递”一个对象引用，因此可以将 API 设计 得更加简洁并且易于复用

this 在任何情况下都不指向函数的词法作用域。在 JavaScript 内部，作用 域确实和对象类似，可见的标识符都是它的属性。但是作用域“对象”无法通过 JavaScript 代码访问，它存在于 JavaScript 引擎内部。

```js
function foo() {
  var a = 2;
  this.bar();
}
function bar() {
  console.log(this.a);
}
foo(); // ReferenceError: a is not defined
```

不要把this的指向和词法作用域混淆

**this 是在运行时进行绑定的，并不是在编写时绑定，它的上下文取决于函数调 用时的各种条件。this 的绑定和函数声明的位置没有任何关系，只取决于函数的调用方式。**

当一个函数被调用时，会创建一个活动记录（有时候也称为执行上下文）。这个记录会包 含函数在哪里被调用（调用栈）、函数的调用方法、传入的参数等信息。this 就是记录的 其中一个属性，会在函数执行的过程中用到。 

# **this**全面解析

## 调用位置

```js
function baz() {
  // 当前调用栈是：baz // 因此，当前调用位置是全局作用域
  console.log("baz");
  bar(); // <-- bar 的调用位置
}
function bar() {
  // 当前调用栈是 baz -> bar // 因此，当前调用位置在 baz 中
  console.log("bar");
  foo(); // <-- foo 的调用位置
}
function foo() {
  // 当前调用栈是 baz -> bar -> foo // 因此，当前调用位置在 bar 中
  console.log("foo");
}
baz(); // <-- baz 的调用位置

```

你可以把调用栈想象成一个函数调用链，就像我们在前面代码段的注释中所 写的一样。但是这种方法非常麻烦并且容易出错。另一个查看调用栈的方法 是使用浏览器的调试工具。绝大多数现代桌面浏览器都内置了开发者工具， 其中包含 JavaScript 调试器。就本例来说，你可以在工具中给 foo() 函数的 第一行代码设置一个断点，或者直接在第一行代码之前插入一条 debugger; 语句。运行代码时，调试器会在那个位置暂停，同时会展示当前位置的函数 调用列表，这就是你的调用栈。因此，如果你想要分析 this 的绑定，使用开 发者工具得到调用栈，然后找到栈中第二个元素，这就是真正的调用位置

## 绑定规则

### 默认绑定 

最常用的函数调用类型：独立函数调用。可以把这条规则看作是无法应用 其他规则时的默认规则。

```js
function foo() {
  console.log(this.a);
}
var a = 2;
foo(); // 2
```

你应该注意到的第一件事是，声明在全局作用域中的变量（比如 var a = 2）就是全局对 象的一个同名属性。它们本质上就是同一个东西，并不是通过复制得到的

如果使用严格模式（strict mode），那么全局对象将无法使用默认绑定，因此 this 会绑定 到 undefined：

```js
function foo() {
  "use strict";
  console.log(this.a);
}
var a = 2;
foo(); // TypeError: this is undefined
```

虽然 this 的绑定规则完全取决于调用位置，但是只 有 foo() 运行在非 strict mode 下时，默认绑定才能绑定到全局对象；严格模式下与 foo() 的调用位置无关：

```js
function foo() {
  console.log(this.a);
}
var a = 2;
(function () {
  "use strict";
  foo(); // 2
})();
```

### 隐式绑定

```js
function foo() {
  console.log(this.a);
}
var obj = { a: 2, foo: foo };
obj.foo(); // 2

```

首先需要注意的是 foo() 的声明方式，及其之后是如何被当作引用属性添加到 obj 中的。 但是无论是直接在 obj 中定义还是先定义再添加为引用属性，这个函数严格来说都不属于 obj 对象。然而，调用位置会使用 obj 上下文来引用函数，因此你可以说函数被调用时 obj 对象“拥 有”或者“包含”它。无论你如何称呼这个模式，当 foo() 被调用时，它的落脚点确实指向 obj 对象。当函数引 用有上下文对象时，隐式绑定规则会把函数调用中的 this 绑定到这个上下文对象。因为调 用 foo() 时 this 被绑定到 obj，因此 this.a 和 obj.a 是一样的

对象属性引用链中只有最顶层或者说最后一层会影响调用位置

```js
function foo() {
  console.log(this.a);
}
var obj2 = { a: 42, foo: foo };
var obj1 = { a: 2, obj2: obj2 };
obj1.obj2.foo(); // 42
```

一个最常见的 this 绑定问题就是被隐式绑定的函数会丢失绑定对象，也就是说它会应用默认绑定，从而把 this 绑定到全局对象或者undefined 上，取决于是否是严格模式

```js
function foo() {
  console.log(this.a);
}
var obj = { a: 2, foo: foo };
var bar = obj.foo; // 函数别名！
var a = "oops, global"; // a 是全局对象的属性
bar(); // "oops, global"
```

虽然 bar 是 obj.foo 的一个引用，但是实际上，它引用的是 foo 函数本身，因此此时的 bar() 其实是一个不带任何修饰的函数调用，因此应用了默认绑定

一种更微妙、更常见并且更出乎意料的情况发生在传入回调函数时：

```js
function foo() {
  console.log(this.a);
}
function doFoo(fn) {
  // fn 其实引用的是 foo
  fn(); // <-- 调用位置！
}
var obj = { a: 2, foo: foo };
var a = "oops, global"; // a 是全局对象的属性
doFoo(obj.foo); // "oops, global"
```

参数传递其实就是一种隐式赋值，因此我们传入函数时也会被隐式赋值，所以结果和上一 个例子一样。 

如果把函数传入语言内置的函数而不是传入你自己声明的函数，会发生什么呢？结果是一 样的，没有区别：

```js
function foo() {
  console.log(this.a);
}
var obj = { a: 2, foo: foo };
var a = "oops, global"; // a 是全局对象的属性
setTimeout(obj.foo, 100); // "oops, global"
```

JavaScript 环境中内置的 setTimeout() 函数实现和下面的伪代码类似：

```
function** setTimeout(fn,delay) { 

// 等待 delay 毫秒 

fn(); // <-- 调用位置！ 

}
```

### 显式绑定

```js
function foo() {
  console.log(this.a);
}
var obj = { a: 2 };
foo.call(obj); // 2
```

通过 foo.call(..)，我们可以在调用 foo 时强制把它的 this 绑定到 obj 上如果你传入了一个原始值（字符串类型、布尔类型或者数字类型）来当作 this 的绑定对 象，这个原始值会被转换成它的对象形式（也就是 new String(..)、new Boolean(..) 或者 new Number(..)）。这通常被称为“装箱”。

显式绑定仍然无法解决我们之前提出的丢失绑定问题

#### 硬绑定

但是显式绑定的一个变种可以解决这个问题

```js
function foo() {
  console.log(this.a);
}
var obj = { a: 2 };
var bar = function () {
  foo.call(obj);
};
bar(); // 2 
setTimeout(bar, 100); // 2
// 硬绑定的 bar 不可能再修改它的 this
bar.call(window); //2
```

我们创建了函数 bar()，并在它的内部手动调用 了 foo.call(obj)，因此强制把 foo 的 this 绑定到了 obj。无论之后如何调用函数 bar，它 

总会手动在 obj 上调用 foo。这种绑定是一种显式的强制绑定，因此我们称之为硬绑定

硬绑定的典型应用场景就是创建一个包裹函数，传入所有的参数并返回接收到的所有值：

```js

function foo(something) {
  console.log(this.a, something);
  return this.a + something;
}
var obj = { a: 2 };
var bar = function () {
  return foo.apply(obj, arguments);
};
var b = bar(3); // 2 3
console.log(b); // 5
```

另一种使用方法是创建一个 i可以重复使用的辅助函数：

```js
function foo(something) {
  console.log(this.a, something);
  return this.a + something;
} // 简单的辅助绑定函数
function bind(fn, obj) {
  return function () {
    return fn.apply(obj, arguments);
  };
}
var obj = { a: 2 };
var bar = bind(foo, obj);
var b = bar(3); // 2 3
console.log(b); // 5
```

由于硬绑定是一种非常常用的模式，所以在 ES5 中提供了内置的方法 Function.prototype. bind，它的用法如下：

```js
function foo(something) {
  console.log(this.a, something);
  return this.a + something;
}
var obj = { a: 2 };
var bar = foo.bind(obj);
var b = bar(3); // 2 3
console.log(b); // 5
```

bind(..) 会返回一个硬编码的新函数，它会把参数设置为 this 的上下文并调用原始函数。 

#### API调用的“上下文” 

第三方库的许多函数，以及 JavaScript 语言和宿主环境中许多新的内置函数，都提供了一 个可选的参数，通常被称为“上下文”（

context），其作用和 bind(..) 一样，确保你的回调 函数使用指定的 this。

```js
function foo(el) {
  console.log(el, this.id);
}
var obj = { id: "awesome" }; // 调用 foo(..) 时把 this 绑定到 obj
[1, 2, 3].forEach(foo, obj); // 1 awesome 2 awesome 3 awesome
```

这些函数实际上就是通过 call(..) 或者 apply(..) 实现了显式绑定，这样你可以少些一些 代码。 

### new绑定

在传统的面向类的语言中，“构造函数”是类中的一些特殊方法，使用 new 初始化类时会 调用类中的构造函数。通常的形式是这样的：

```
something = new MyClass(..);
```

JavaScript 也有一个 new 操作符，使用方法看起来也和那些面向类的语言一样，绝大多数开 发者都认为 JavaScript 中 new 的机制也和那些语言一样。然而，JavaScript 中 new 的机制实 际上和面向类的语言完全不同

首先我们重新定义一下 JavaScript 中的“构造函数”。在 JavaScript 中，构造函数只是一些 使用 new 操作符时被调用的函数。它们并不会属于某个类，也不会实例化一个类。实际上， 它们甚至都不能说是一种特殊的函数类型，它们只是被 new 操作符调用的普通函数而已。

举例来说，思考一下 Number(..) 作为构造函数时的行为，ES5.1 中这样描述它： 15.7.2 Number 构造函数 当 Number 在 new 表达式中被调用时，它是一个构造函数：它会初始化新创建的 对象。 所以，包括内置对象函数（比如 Number(..)，详情请查看第 3 章）在内的所有函数都可 以用 new 来调用，这种函数调用被称为构造函数调用。这里有一个重要但是非常细微的区 别：实际上并不存在所谓的“构造函数”，只有对于函数的“构造调用”。

使用 new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。 

1. 创建（或者说构造）一个全新的对象。 

2. 这个新对象会被执行 [[ 原型 ]] 连接。 

3. 这个新对象会绑定到函数调用的 this。 

4. 如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象。

## 优先级

**默认绑定的优先级是四条规则中最低的**

```js
function foo() {
  console.log(this.a);
}
var obj1 = { a: 2, foo: foo };
var obj2 = { a: 3, foo: foo };
obj1.foo(); // 2
obj2.foo(); // 3
obj1.foo.call(obj2); // 3
obj2.foo.call(obj1); // 2

```

**显示绑定的优先级高于隐式绑定**

```js
function foo(something) {
  this.a = something;
}
var obj1 = { foo: foo };
var obj2 = {};
obj1.foo(2);
console.log(obj1.a); // 2
obj1.foo.call(obj2, 3);
console.log(obj2.a); // 3
var bar = new obj1.foo(4);
console.log(obj1.a); // 2
console.log(bar.a); // 4
```

**new 绑定比隐式绑定优先级高**

new 和 call/apply 无法一起使用，因此无法通过 new foo.call(obj1) 来直接 进行测试。但是我们可以使用硬绑定来测试它俩的优先级

在看代码之前先回忆一下硬绑定是如何工作的。Function.prototype.bind(..) 会创建一个 新的包装函数，这个函数会忽略它当前的 this 绑定（无论绑定的对象是什么），并把我们 提供的对象绑定到 this 上。

这样看起来硬绑定（也是显式绑定的一种）似乎比 new 绑定的优先级更高，无法使用 new 来控制 this 绑定。

```js
function foo(something) {
  this.a = something;
}
var obj1 = {};
var bar = foo.bind(obj1);
bar(2);
console.log(obj1.a); // 2
var baz = new bar(3);
console.log(obj1.a); // 2
console.log(baz.a); // 3
```

bar 被硬绑定到 obj1 上，但是 new bar(3) 并没有像我们预计的那样把 obj1.a 修改为 3。相反，new 修改了硬绑定（到 obj1 的）调用 bar(..) 中的 this。因为使用了 new 绑定，我们得到了一个名字为 baz 的新对象，并且 baz.a 的值是 3。

ES5 中内置的 Function.prototype.bind(..) 更加复杂。下面是 MDN 提供的一种 bind(..) 实现，为了方便阅读我们对代码进行了排版：

```js
if (!Function.prototype.bind) {
  Function.prototype.bind = function (oThis) {
    if (typeof this !== "function") {
      // 与 ECMAScript 5 最接近的 // 内部 IsCallable 函数
      throw new TypeError(
        "Function.prototype.bind - what is trying " +
          "to be bound is not callable"
      );
    }
    var aArgs = Array.prototype.slice.call(arguments, 1),
      fToBind = this,
      fNOP = function () {},
      fBound = function () {
        return fToBind.apply(
          this instanceof fNOP && oThis ? this : oThis,
          aArgs.concat(Array.prototype.slice.call(arguments))
        );
      };
    fNOP.prototype = this.prototype;
    fBound.prototype = new fNOP();
    return fBound;
  };
}
```

polyfill 代码主要用于旧浏览器的兼容，比如说在旧的浏览器中并没 有内置 bind 函数，因此可以使用 polyfill 代码在旧浏览器中实现新的功 能），对于 new 使用的硬绑定函数来说，这段 polyfill 代码和 ES5 内置的 bind(..) 函数并不完全相同（后面会介绍为什么要在 new 中使用硬绑定函 数）。由于 polyfill 并不是内置函数，所以无法创建一个不包含 .prototype 的函数，因此会具有一些副作用。如果你要在 new 中使用硬绑定函数并且依 赖 polyfill 代码的话，一定要非常小心

下面是 new 修改 this 的相关代码：

```js
this instanceof fNOP && oThis ? this : oThis 
// ... 以及： 
fNOP.prototype = this.prototype; fBound.prototype = new fNOP();
```

这段代码会判断硬绑定函数是否是被 new 调用，如果是的话就会使用新创建 的 this 替换硬绑定的 this。

那么，为什么要在 new 中使用硬绑定函数呢？直接使用普通函数不是更简单吗？

之所以要在 new 中使用硬绑定函数，主要目的是预先设置函数的一些参数，这样在使用 new 进行初始化时就可以只传入其余的参数。bind(..) 的功能之一就是可以把除了第一个 参数（第一个参数用于绑定 this）之外的其他参数都传给下层的函数（这种技术称为“部 分应用”，是“柯里化”的一种）。

```js
function foo(p1, p2) {
  this.val = p1 + p2;
} // 之所以使用 null 是因为在本例中我们并不关心硬绑定的 this 是什么 // 反正使用 new 时 this 会被修改
var bar = foo.bind(null, "p1");
var baz = new bar("p2");
baz.val; // p1p2
```

#### 判断this 

1. 函数是否在 new 中调用（new 绑定）？如果是的话 this 绑定的是新创建的对象。 

var bar = new foo() 

2. 函数是否通过 call、apply（显式绑定）或者硬绑定调用？如果是的话，this 绑定的是 指定的对象。 

var bar = foo.call(obj2) 

3. 函数是否在某个上下文对象中调用（隐式绑定）？如果是的话，this 绑定的是那个上 下文对象。 

var bar = obj1.foo() 

4. 如果都不是的话，使用默认绑定。如果在严格模式下，就绑定到 undefined，否则绑定到 全局对象。 

var bar = foo() 

## 绑定例外

如果你把 null 或者 undefined 作为 this 的绑定对象传入 call、apply 或者 bind，这些值 在调用时会被忽略，实际应用的是默认绑定规则：

```js
function foo() {
  console.log(this.a);
}
var a = 2;
foo.call(null); // 2
```

一种非常常见的做法是使用 apply(..) 来“展开”一个数组，并当作参数传入一个函数。 类似地，bind(..) 可以对参数进行柯里化（预先设置一些参数），这种方法有时非常有用：

```js
function foo(a, b) {
  console.log("a:" + a + ", b:" + b);
} // 把数组“展开”成参数 foo.apply( null, [2, 3] ); // a:2, b:3
// 使用 bind(..) 进行柯里化
var bar = foo.bind(null, 2);
bar(3); // a:2, b:3
```

可以用 ... 操作符代替 apply(..) 来“展 开”数组，foo(...[1,2]) 和 foo(1,2) 是一样的，这样可以避免不必要的 this 绑定。可惜，在 ES6 中没有柯里化的相关语法，因此还是需要使用 bind(..)。 

一种“更安全”的做法是传入一个特殊的对象，把 this 绑定到这个对象不会对你的程序 然而，总是使用 null 来忽略 this 绑定可能产生一些副作用。如果某个函数确实使用了 this（比如第三方库中的一个函数），那默认绑定规则会把 this 绑定到全局对象（在浏览 器中这个对象是 window），这将导致不可预计的后果（比如修改全局对象）。

一种“更安全”的做法是传入一个特殊的对象，把 this 绑定到这个对象不会对你的程序 产生任何副作用

```js
function foo(a, b) {
  console.log("a:" + a + ", b:" + b);
} // 我们的 DMZ 空对象
var ø = Object.create(null); // 把数组展开成参数
foo.apply(ø, [2, 3]); // a:2, b:3 // 使用 bind(..) 进行柯里化
var bar = foo.bind(ø, 2);
bar(3); // a:2, b:3
```

另一个需要注意的是，你有可能（有意或者无意地）创建一个函数的“间接引用”，在这 种情况下，调用这个函数会应用默认绑定规则。

间接引用最容易在赋值时发生：

```js
var a = 2;
var o = { a: 3, foo: foo };
var p = { a: 4 };
o.foo(); // 3
(p.foo = o.foo)(); // 2
```

赋值表达式 p.foo = o.foo 的返回值是目标函数的引用，因此调用位置是 foo() 而不是 p.foo() 或者 o.foo()。根据我们之前说过的，这里会应用默认绑定。 注意：对于默认绑定来说，决定 this 绑定对象的并不是调用位置是否处于严格模式，而是 函数体是否处于严格模式。如果函数体处于严格模式，this 会被绑定到 undefined，否则 this 会被绑定到全局对象。 

### 软绑定

之前我们已经看到过，硬绑定这种方式可以把 this 强制绑定到指定的对象（除了使用 new 时），防止函数调用应用默认绑定规则。问题在于，硬绑定会大大降低函数的灵活性，使 用硬绑定之后就无法使用隐式绑定或者显式绑定来修改 this。

如果可以给默认绑定指定一个全局对象和 undefined 以外的值，那就可以实现和硬绑定相 同的效果，同时保留隐式绑定或者显式绑定修改 this 的能力。可以通过一种被称为软绑定的方法来实现我们想要的效果：

```js
if (!Function.prototype.softBind) {
  Function.prototype.softBind = function (obj) {
    var fn = this; // 捕获所有 curried 参数
    var curried = [].slice.call(arguments, 1);
    var bound = function () {
      return fn.apply(
        !this || this === (window || global) ? obj : this,
        curried.concat.apply(curried, arguments)
      );
    };

    bound.prototype = Object.create(fn.prototype);
    return bound;
  };
}
```

除了软绑定之外，softBind(..) 的其他原理和 ES5 内置的 bind(..) 类似。它会对指定的函 数进行封装，首先检查调用时的 this，如果 this 绑定到全局对象或者 undefined，那就把 指定的默认对象 obj 绑定到 this，否则不会修改 this。此外，这段代码还支持可选的柯里 化

下面我们看看 softBind 是否实现了软绑定功能

```js
function foo() {
  console.log("name: " + this.name);
}
var obj = { name: "obj" },
  obj2 = { name: "obj2" },
  obj3 = { name: "obj3" };
var fooOBJ = foo.softBind(obj);
fooOBJ(); // name: obj
obj2.foo = foo.softBind(obj);
obj2.foo(); // name: obj2 <---- 看！！！
fooOBJ.call(obj3); // name: obj3 <---- 看！
setTimeout(obj2.foo, 10); // name: obj <---- 应用了软绑定
```

可以看到，软绑定版本的 foo() 可以手动将 this 绑定到 obj2 或者 obj3 上，但如果应用默 认绑定，则会将 this 绑定到 obj。

### this词法

箭头函数并不是使用 function 关键字定义的，而是使用被称为“胖箭头”的操作符 => 定 义的。箭头函数不使用 this 的四种标准规则，而是根据外层（函数或者全局）作用域来决 定 this。

```js
function foo() {
  // 返回一个箭头函数
  return (a) => {
    //this 继承自 foo()
    console.log(this.a);
  };
}
var obj1 = { a: 2 };
var obj2 = { a: 3 };
var bar = foo.call(obj1);
bar.call(obj2); // 2, 不是 3 ！
```

foo() 内部创建的箭头函数会捕获调用时 foo() 的 this。由于 foo() 的 this 绑定到 obj1， bar（引用箭头函数）的 this 也会绑定到 obj1，箭头函数的绑定无法被修改。（new 也不 行！）

```js
function foo() {
  setTimeout(() => {
    // 这里的 this 在此法上继承自 foo()
    console.log(this.a);
  }, 100);
}
var obj = { a: 2 };
foo.call(obj); // 2
```

箭头函数可以像 bind(..) 一样确保函数的 this 被绑定到指定对象，此外，其重要性还体 现在它用更常见的词法作用域取代了传统的 this 机制。实际上，在 ES6 之前我们就已经 在使用一种几乎和箭头函数完全一样的模式。

```js
function foo() {
  var self = this; // lexical capture of this
  setTimeout(function () {
    console.log(self.a);
  }, 100);
}
var obj = { a: 2 };
foo.call(obj); // 2
```

虽然 self = this 和箭头函数看起来都可以取代 bind(..)，但是从本质上来说，它们想替 代的是 this 机制

如果你经常编写 this 风格的代码，但是绝大部分时候都会使用 self = this 或者箭头函数 来否定 this 机制，那你或许应当：

1. 只使用词法作用域并完全抛弃错误 this 风格的代码； 

2. 完全采用 this 风格，在必要时使用 bind(..)，尽量避免使用 self = this 和箭头函数。

# 对象
