---
title: webpack笔记
date: 2023-04-09 21:20:02
tags:
- [webpack]
categories:
- [常用构建工具,webpack]
---

## Mode配置

Mode配置选项，可以告知webpack使用相应模式的内置优化：

- 默认值是production（什么都不设置的情况下）；
- 可选值有：'none' | 'development' | 'production'；

mode选项的区别：

| 选项        | 描述                                                         |
| ----------- | ------------------------------------------------------------ |
| development | 会将 `DefinePlugin` 中 `process.env.NODE_ENV` 的值设置为 `development`. 为模块和 chunk 启用有效的名。 |
| production  | 会将 `DefinePlugin` 中 `process.env.NODE_ENV` 的值设置为 `production`。为模块和 chunk 启用确定性的混淆名称，`FlagDependencyUsagePlugin`，`FlagIncludedChunksPlugin`，`ModuleConcatenationPlugin`，`NoEmitOnErrorsPlugin` 和 `TerserPlugin` 。 |
| none        | 不使用任何默认优化选项                                       |

## source-map

我们的代码通常运行在浏览器上时，是通过打包压缩的：

- 也就是真实跑在浏览器上的代码，和我们编写的代码其实是有差异的；
- 比如ES6的代码可能被转换成ES5； 
- 比如对应的代码行号、列号在经过编译后肯定会不一致；
- 比如代码进行丑化压缩时，会将编码名称等修改；
- 比如我们使用了TypeScript等方式编写的代码，最终转换成JavaScript；

但是，当代码报错需要调试时（debug），调试转换后的代码是很困难的

但是我们能保证代码不出错吗？不可能。

那么如何可以调试这种转换后不一致的代码呢？答案就是source-map

- source-map是从已转换的代码，映射到原始的源文件； 
- 使浏览器可以重构原始源并在调试器中显示重建的原始源；

### 如何使用source-map

如何可以使用source-map呢？两个步骤：

第一步：根据源文件，生成source-map文件，webpack在打包时，可以通过配置生成source-map； 

第二步：在转换后的代码，最后添加一个注释，它指向sourcemap； 

```
//# sourceMappingURL=common.bundle.js.map
```

浏览器会根据我们的注释，查找相应的source-map，并且根据source-map还原我们的代码，方便进行调试。

在Chrome中，我们可以按照如下的方式打开source-map：

- source-map文件的属性：
- version：当前使用的版本，也就是最新的第三版；
- sources：从哪些文件转换过来的source-map和打包的代码（最初始的文件）；
- names：转换前的变量和属性名称（因为我目前使用的是development模式，所以不需要保留转换前的名称）；
- mappings：source-map用来和源文件映射的信息（比如位置信息等），一串base64 VLQ（veriable-length quantity可变
- 长度值）编码；
- file：打包后的文件（浏览器加载的文件）；
- sourceContent：转换前的具体代码信息（和sources是对应的关系）；
- sourceRoot：所有的sources相对的根目录；

### 生成source-map

在webpack的配置文件中的devtool这个属性中进行配置，来生成不同的source-map。他的打包的性能也会有差异

下面几个值不会生成source-map：

**false：**不使用source-map，也就是没有任何和source-map相关的内容。

**none：**production模式下的默认值（什么值都不写） ，不生成source-map。 

**eval：**development模式下的默认值，不生成source-map

- 但是它会在eval执行的代码中，添加 //# sourceURL=； 
- 它会被浏览器在执行时解析，并且在调试面板中生成对应的一些文件目录，方便我们调试代码

### 对于开发环境

以下选项非常适合开发环境：

`eval` - 每个模块都使用 `eval()` 执行，并且都有 `//# sourceURL`。此选项会非常快地构建。主要缺点是，由于会映射到转换后的代码，而不是映射到原始代码（没有从 loader 中获取 source map），所以不能正确的显示行数。

`eval-source-map` - 每个模块使用 `eval()` 执行，并且 source map 转换为 DataUrl 后添加到 `eval()` 中。初始化 source map 时比较慢，但是会在重新构建时提供比较快的速度，并且生成实际的文件。行数能够正确映射，因为会映射到原始代码中。它会生成用于开发环境的最佳品质的 source map。

`eval-cheap-source-map` - 类似 `eval-source-map`，每个模块使用 `eval()` 执行。这是 "cheap(低开销)" 的 source map，因为它没有生成列映射(column mapping)，只是映射行数。它会忽略源自 loader 的 source map，并且仅显示转译后的代码，就像 `eval` devtool。

`eval-cheap-module-source-map` - 类似 `eval-cheap-source-map`，并且，在这种情况下，源自 loader 的 source map 会得到更好的处理结果。然而，loader source map 会被简化为每行一个映射(mapping)。

### 特定场景

以下选项对于开发环境和生产环境并不理想。他们是一些特定场景下需要的，例如，针对一些第三方工具。

`inline-source-map` - source map 转换为 DataUrl 后添加到 bundle 中。

`cheap-source-map` - 没有列映射(column mapping)的 source map，忽略 loader source map。

`inline-cheap-source-map` - 类似 `cheap-source-map`，但是 source map 转换为 DataUrl 后添加到 bundle 中。

`cheap-module-source-map` - 没有列映射(column mapping)的 source map，将 loader source map 简化为每行一个映射(mapping)。

`inline-cheap-module-source-map` - 类似 `cheap-module-source-map`，但是 source mapp 转换为 DataUrl 添加到 bundle 中。

### 对于生产环境

这些选项通常用于生产环境中：

`(none)`（省略 `devtool` 选项） - 不生成 source map。这是一个不错的选择。

`source-map` - 整个 source map 作为一个单独的文件生成。它为 bundle 添加了一个引用注释，以便开发工具知道在哪里可以找到它。



`nosources-source-map` -会生成sourcemap，但是生成的sourcemap只有错误信息的提示，不会生成源代码文件； 

`hidden-source-map`会生成sourcemap，但是不会对source-map文件进行引用； 相当于删除了打包文件中对sourcemap的引用注释； 

```
// 被删除掉的
//# sourceMappingURL=bundle.js.map
```

如果我们手动添加进来，那么sourcemap就会生效了

`cheap-source-map`会生成sourcemap，但是会更加高效一些（cheap低开销），因为它没有生成列映射（Column Mapping）  

因为在开发中，我们只需要行信息通常就可以定位到错误了

## Babel

Babel是一个工具链，主要用于旧浏览器或者缓解中将ECMAScript 2015+代码转换为向后兼容版本的JavaScript； 

包括：语法转换、源代码转换、Polyfill实现目标环境缺少的功能等；

babel本身可以作为一个独立的工具（和postcss一样），不和webpack等构建工具配置来单独使用。

如果我们希望在命令行尝试使用babel，需要安装如下库：

@babel/core：babel的核心代码，必须安装；

@babel/cli：可以让我们在命令行使用babel； 

```
npm install @babel/cli @babel/core
```

使用babel来处理我们的源代码：

src：是源文件的目录；

--out-dir：指定要输出的文件夹dist；

```
npx babel src --out-dir dist
```

**比如我们需要转换箭头函数，那么我们就可以使用箭头函数转换相关的插件：**

```
npm install @babel/plugin-transform-arrow-functions -D

npx babel src --out-dir dist --plugins=@babel/plugin-transform-arrow-functions
```

**查看转换后的结果：我们会发现 const 并没有转成** 

这是因为 plugin-transform-arrow-functions，并没有提供这样的功能；

我们需要使用 plugin-transform-block-scoping 来完成这样的功能；

```
npm install @babel/plugin-transform-block-scoping -D
npx babel src --out-dir dist --plugins=@babel/plugin-transform-block-scoping
,@babel/plugin-transform-arrow-functions
```

**但是如果要转换的内容过多，一个个设置是比较麻烦的，我们可以使用预设（preset）**

```
npm install @babel/preset-env -D
```

**babel是如何做到将我们的一段代码（ES6、TypeScript、React）转成另外一段代码（ES5）的呢？**

从一种源代码（原生语言）转换成另一种源代码（目标语言），这是什么的工作呢？

就是**编译器**，事实上我们可以将babel看成就是一个编译器。 

Babel编译器的作用就是将我们的源代码，转换成浏览器可以直接识别的另外一段源代码； 

**Babel也拥有编译器的工作流程：**

解析阶段（Parsing） 

转换阶段（Transformation） 

生成阶段（Code Generation） 



别人用js写的编译器

https://github.com/jamiebuilds/the-super-tiny-compiler

**babel编译器执行原理**

源代码->解析->转换->代码生成->目标代码

源代码->词法分析->tokens数组->语法分析->AST树-	>遍历->访问->应用插件->新的AST树->目标代码

### babel-loader

在webpack中会使用babel-loader

```
npm install babel-loader @babel/core
```

webpack.config.js

```js
module: {
    rules: [
      {
        test: /\.m?js/,
        use: "babel-loader",
      },
    ],
  },
```

需要使用相应插件时才生效

```js
{
        test: /\.m?js/,
        use: "babel-loader",
        options: {
          plugins: [
            "@babel/plugin-transform-arrow-functions",
            "@babel/plugin-transform-block-scoping",
          ],
        },
      },
```

### babel-preset

**如果我们一个个去安装使用插件，那么需要手动来管理大量的babel插件，我们可以直接给webpack提供一个preset，webpack会根据我们的预设来加载对应的插件列表，并且将其传递给babel**

**比如常见的预设有三个：**

env

react

TypeScript

```
npm install @babel/preset-env
```



```js
{
        test: /\.m?js/,
        use: "babel-loader",
        options: {
          presets: [["@babel/preset-env"]],
        },
      },
```

### 浏览器兼容性

**但是在哪里可以查询到浏览器的市场占有率呢？**

https://caniuse.com/usage-table

使用browserlist可以在css兼容性和js兼容性下共享我们配置的兼容性条件

Browserslist是一个在不同的前端工具之间，共享目标浏览器和Node.js版本的配置

- Autoprefixer
- Babel
- postcss-preset-env
- eslint-plugin-compat
- stylelint-no-unsupported-browser-features
- postcss-normalize
- obsolete-webpack-plugin

#### 

browserlist可以替代babel的target

browserlist可以在package.json中写

```json
"browserslist": [
    "defaults and supports es6-module",
    "maintained node versions"
  ]
```

也可以创建.browserslistrc文件

```
> 0.1%
last 2 versions
not dead
```

Browserslist编写规则一：

**defaults：Browserslist的默认浏览器（> 0.5%, last 2 versions, Firefox ESR, not dead）。**

**5%：通过全局使用情况统计信息选择的浏览器版本。 >=，<和<=工作过。**

5% in US：使用美国使用情况统计信息。它接受两个字母的国家/地区代码。

5% in alt-AS：使用亚洲地区使用情况统计信息。有关所有区域代码的列表，请参见caniuse-lite/data/regions

5% in my stats：使用自定义用法数据。

5% in browserslist-config-mycompany stats：使用 来自的自定义使用情况数据browserslist-config-mycompany/browserslist-stats.json。 

cover 99.5%：提供覆盖率的最受欢迎的浏览器。

cover 99.5% in US：与上述相同，但国家/地区代码由两个字母组成。

cover 99.5% in my stats：使用自定义用法数据。

**dead：24个月内没有官方支持或更新的浏览器。现在是IE 10，IE_Mob 11，BlackBerry 10，BlackBerry 7， Samsung 4和OperaMobile 12.1。** 

**last 2 versions：每个浏览器的最后2个版本。**

last 2 Chrome versions：最近2个版本的Chrome浏览器。

last 2 major versions或last 2 iOS major versions：最近2个主要版本的所有次要/补丁版本。

Browserslist编写规则二：

**node 10和node 10.4：选择最新的Node.js10.x.x 或10.4.x版本。**

current node：Browserslist现在使用的Node.js版本。

maintained node versions：所有Node.js版本，仍由 Node.js Foundation维护。

**iOS 7：直接使用iOS浏览器版本7。** 

Firefox > 20：Firefox的版本高于20 >=，<并且<=也可以使用。它也可以与Node.js一起使用。

ie 6-8：选择一个包含范围的版本。

Firefox ESR：最新的[Firefox ESR]版本。

PhantomJS 2.1和PhantomJS 1.9：选择类似于PhantomJS运行时的Safari版本。

**extends browserslist-config-mycompany：从browserslist-config-mycompanynpm包中查询 。** 

**supports es6-module：支持特定功能的浏览器。**

es6-module这是“我可以使用” 页面feat的URL上的参数。有关所有可用功能的列表，请参见 。caniuse-lite/data/features

**browserslist config：在Browserslist配置中定义的浏览器。**在差异服务中很有用，可用于修改用户的配置，例如 browserslist config and supports es6-module。 

**since 2015或last 2 years**：自2015年以来发布的所有版本（since 2015-03以及since 2015-03-10）。

**unreleased versions或unreleased Chrome versions**：Alpha和Beta版本。

**not ie <= 8：排除先前查询选择的浏览器。**

命令行使用browserslist

```
npx browserslist ">1%, last 2 version, not dead"
```

#### 配置browserslist

方案一：在package.json中配置；

方案二：单独的一个配置文件.browserslistrc文件

如果install 之后没有配置browserlist，他会有个默认的配置

```
> 0.5%, last 2 versions, Firefox ESR, not dead
```

不同的查询条件的意义

| Query combiner type                  | Illustration                                                 | Example                                                      |
| ------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `or`/`,` combiner (union)            | [![Union of queries](https://github.com/browserslist/browserslist/raw/main/img/union.svg)](https://github.com/browserslist/browserslist/blob/main/img/union.svg) | `> .5% or last 2 versions` `> .5%, last 2 versions`          |
| `and` combiner (intersection)        | [![intersection of queries](https://github.com/browserslist/browserslist/raw/main/img/intersection.svg)](https://github.com/browserslist/browserslist/blob/main/img/intersection.svg) | `> .5% and last 2 versions`                                  |
| `not` combiner (relative complement) | [![Relative complement of queries](https://github.com/browserslist/browserslist/raw/main/img/complement.svg)](https://github.com/browserslist/browserslist/blob/main/img/complement.svg) | These three are equivalent to one another: `> .5% and not last 2 versions` `> .5% or not last 2 versions` `> .5%, not last 2 versions` |

#### 设置目标浏览器 browserslist

**我们最终打包的JavaScript代码，是需要跑在目标浏览器上的，那么如何告知babel我们的目标浏览器呢？**

browserslist工具

target属性

#### 设置目标浏览器 targets

**我们也可以通过targets来进行配置：**

webpack.config.js

```js
 {
        test: /\.jsx?$/, // x?: 0或者1个x
        // exclude: /node_modules/,
        use: {
          loader: "babel-loader",
            options: {
              presets: [
                ["@babel/preset-env", {
                  // 在开发中针对babel的浏览器兼容查询使用browserslist工具, 而不是设置target
                  // 因为browserslist工具, 可以在多个前端工具之间进行共享浏览器兼容性(postcss/babel)
                  targets: ">5%"
                }]
              ]
            }
        },
      },
```

**那么，如果两个同时配置了，哪一个会生效呢？**

配置的targets属性会覆盖browserslist； 

但是在开发中，更推荐通过browserslist来配置，因为类似于postcss工具，也会使用browserslist，进行统一浏览器的适配；

### Stage-X的preset

**要了解Stage-X，我们需要先了解一下TC39的组织：**

TC39是指技术委员会（Technical Committee）第 39 号；

它是 ECMA 的一部分，ECMA 是 “ECMAScript” 规范下的 JavaScript 语言标准化的机构；

ECMAScript 规范定义了 JavaScript 如何一步一步的进化、发展；

**TC39 遵循的原则是：分阶段加入不同的语言特性，新流程涉及四个不同的 Stage**

**Stage 0：**strawman（稻草人），任何尚未提交作为正式提案的讨论、想法变更或者补充都被认为是第 0 阶段的"稻草人"； 

**Stage 1：**proposal（提议），提案已经被正式化，并期望解决此问题，还需要观察与其他提案的相互影响；

**Stage 2：**draft（草稿），Stage 2 的提案应提供规范初稿、草稿。此时，语言的实现者开始观察 runtime 的具体实现是否

合理；

**Stage 3：**candidate（候补），Stage 3 提案是建议的候选提案。在这个高级阶段，规范的编辑人员和评审人员必须在最终

规范上签字。Stage 3 的提案不会有太大的改变，在对外发布之前只是修正一些问题；

**Stage 4：**finished（完成），进入 Stage 4 的提案将包含在 ECMAScript 的下一个修订版中；

### Babel的Stage-X设置

**在babel7之前（比如babel6中），我们会经常看到这种设置方式：**

它表达的含义是使用对应的 babel-preset-stage-x 预设；

但是从babel7开始，已经不建议使用了，建议使用preset-env来设置；

```
module.exports={
	'presets': ['stage-0'];
}
```

### Babel的配置文件

**我们可以将babel的配置信息放到一个独立的文件中，babel给我们提供了两种配置文件的编写：**

babel.config.json（或者.js，.cjs，.mjs）文件；

.babelrc.json（或者.babelrc，.js，.cjs，.mjs）文件；

**它们两个有什么区别呢？目前很多的项目都采用了多包管理的方式（babel本身、element-plus、umi等）；**

.babelrc.json：早期使用较多的配置方式，但是对于配置Monorepos项目是比较麻烦的；

babel.config.json（babel7）：可以直接作用于Monorepos项目的子包，更加推荐；

```js
module.exports = {
  // plugins: [
  //   "@babel/plugin-transform-arrow-functions",
  //   "@babel/plugin-transform-block-scoping"
  // ]
  presets: [
    [
      "@babel/preset-env",
      {
        // 在开发中针对babel的浏览器兼容查询使用browserslist工具, 而不是设置target
        // 因为browserslist工具, 可以在多个前端工具之间进行共享浏览器兼容性(postcss/babel)
        // targets: ">5%"
        // corejs: 3,
        // // false: 不使用polyfill进行填充
        // useBuiltIns: "entry"//会对第三方的库进行polyfill
      },
    ],
    ["@babel/preset-react"],
    [
      "@babel/preset-typescript",
      {
        corejs: 3, //下载的版本
        useBuiltIns: "usage", //一般会使用usage
      },
    ],
  ],
};
```

问题：如果在webpack.config.js和babel.config.js都配置了babel。那么最后谁生效了呢

### polyfill

**为什么时候会用到polyfill呢？**

比如我们使用了一些语法特性（例如：Promise, Generator, Symbol等以及实例方法例如Array.prototype.includes等）

但是某些浏览器压根不认识这些特性，必然会报错；

我们可以使用polyfill来填充或者说打一个补丁，那么就会包含该特性了；

**如何使用polyfill**

**babel7.4.0之前，可以使用 @babel/polyfill的包，但是该包现在已经不推荐使用了**

**babel7.4.0之后，可以通过单独引入core-js和regenerator-runtime来完成polyfill的使用**

```
npm install core-js regenerator-runtime --save
```

可以在webpack中配置，也可以在babel的配置文件中进行配置

**我们需要在babel.config.js文件中进行配置，给preset-env配置一些属性：**

useBuiltIns：设置以什么样的方式来使用polyfill； 

corejs：设置corejs的版本，目前使用较多的是3.x的版本，

另外corejs可以设置是否对提议阶段的特性进行支持；

设置 proposals属性为true即可；

**useBuiltIns属性设置**

```
useBuiltIns: 'false'
```

打包后的文件不使用polyfill来进行适配；

并且这个时候是不需要设置corejs属性的；

```
useBuiltIns: 'usage'
```

会根据源代码中出现的语言特性，自动检测所需要的polyfill； 

这样可以确保最终包里的polyfill数量的最小化，打包的包相对会小一些；

可以设置corejs属性来确定使用的corejs的版本；

```js
presets: [
    [
      "@babel/preset-typescript",
      {
        corejs: 3, //下载的版本
        useBuiltIns: "usage", //一般会使用usage
      },
    ],
  ],
```



```
useBuiltIns: 'usage'
```

如果我们依赖的某一个库本身使用了某些polyfill的特性，但是因为我们使用的是usage，所以之后用户浏览器可能会报错；

所以，如果你担心出现这种情况，可以使用 entry； 

并且需要在入口文件中添加 **`import 'core-js/stable'; import 'regenerator-runtime/runtime';**

这样做会根据 browserslist 目标导入所有的polyfill，但是对应的包也会变大；

```
//例如入口文件是index.js
import 'core-js/stable'
import 'regenerator-runtime/runtime'
```

### React的jsx支持

**对react jsx代码进行处理需要如下的插件：**

@babel/plugin-syntax-jsx

@babel/plugin-transform-react-jsx

@babel/plugin-transform-react-display-name

**但是开发中，我们并不需要一个个去安装这些插件，我们依然可以使用preset来配置**

```
npm install @babel/preset-react -D
```



```
presets: [
    [
      "@babel/preset-react",
    ],
  ],
```

## TypeScript的编译

```
npm install typescript -D
```

**另外TypeScript的编译配置信息我们通常会编写一个tsconfig.json文件：**

```
tsc --init
```

**之后我们可以运行 npx tsc来编译自己的ts代码**

```
npx tsc
```

### **使用ts-loader**

在webpack中可以使用ts-loader来处理ts文件

```
npm install ts-loader -D
```



```
{
        test: /\.ts$/,
        use: "ts-loader"
 }
```



#### **使用babel-loader**

**除了可以使用TypeScript Compiler来编译TypeScript之外，我们也可以使用Babel：** 

Babel是有对TypeScript进行支持；

我们可以使用插件： @babel/tranform-typescript； 

但是更推荐直接使用preset：@babel/preset-typescript； 

**我们来安装@babel/preset-typescript：**

```
npm install @babel/preset-typescript -D
```

```js
[
      "@babel/preset-typescript",
      {
        corejs: 3, //下载的版本
        useBuiltIns: "usage", //一般会使用usage
      },
    ],
```

#### ts-loader和babel-loader选择

**使用ts-loader（TypeScript Compiler）** 

来直接编译TypeScript，那么只能将ts转换成js； 

如果我们还希望在这个过程中添加对应的polyfill，那么ts-loader是无能为力的；

我们需要借助于babel来完成polyfill的填充功能；

**使用babel-loader（Babel）** 

来直接编译TypeScript，也可以将ts转换成js，并且可以实现polyfill的功能；

但是babel-loader在编译的过程中，不会对类型错误进行检测；

我们可以在开发阶段使用ts-loader，在构建阶段使用babel-loader

```
 "scripts": {
    "build": "webpack",
    "ts-check": "tsc --noEmit",
    "ts-check-watch": "tsc --noEmit --watch"
  },
```

我们执行 npm run type-check可以对ts代码的类型进行检测；

我们执行 npm run type-check-watch可以实时的检测类型错误；

## 搭建本地服务器

**为了完成自动编译，webpack提供了几种可选的方式：**

webpack watch mode； 

webpack-dev-server（常用）；

webpack-dev-middleware；

#### webpack-dev-server

```
npm install webpack-dev-server -D
```

**修改配置文件，启动时加上serve参数：**

```js
 //webpack-dev-server 不会打包到dist文件夹
  devServer: {
    //静态资源文件夹，在devserver中 ，告诉浏览器静态资源文件去哪里找，如果不写，默认是public文件夹
    //下面的设置是告诉浏览器去content，public文件夹去找
    static: ["public", "content"],
    // 设置主机地址，设置为0.0.0.0可以监听同一网段下的所有地址
    // host: '0.0.0.0',
    hot: only,//启用热模块替换功能，在构建失败时不刷新页面作为回退，使用 hot: 'only'
    port: 3000,
    open: true,
    compress: true,
    proxy: {
      "/api": {
        target: "http://localhost:9000",
        pathRewrite: {
          "^/api": "",
        },
        changeOrigin: true,
      },
    },

    historyApiFallback: true,
  },
```



```
 "serve": "webpack serve",
```

**webpack-dev-server 在编译之后不会写入到任何输出文件，而是将 bundle 文件保留在内存中：**

事实上webpack-dev-server使用了一个库叫memfs（memory-fs webpack自己写的）

#### devServer的static

**devServer中static对于我们直接访问打包后的资源其实并没有太大的作用，它的主要作用是如果我们打包后的资源，又依赖于**

**其他的一些资源，那么就需要指定从哪里来查找这个内容：**

比如在index.html中，我们需要依赖一个 abc.js 文件，这个文件我们存放在 public文件 中；

在index.html中，我们应该如何去引入这个文件呢？

✓比如代码是这样的：<script src="./public/abc.js"></script>； 

✓ 但是这样打包后浏览器是无法通过相对路径去找到这个文件夹的；

✓ 所以代码是这样的：<script src="/abc.js"></script>;

✓ 但是我们如何让它去查找到这个文件的存在呢？ 设置static即可；

#### **hot、host配置**

**hot:only是当代码编译失败时，是否刷新整个页面：**

默认情况下当代码编译失败修复后，我们会重新刷新整个页面；

如果不希望重新刷新整个页面，可以设置hot:only； 

**host设置主机地址：**

默认值是localhost；

如果希望其他地方也可以访问，可以设置为 0.0.0.0； 

**localhost 和 0.0.0.0 的区别：**

localhost：本质上是一个域名，通常情况下会被解析成127.0.0.1;

127.0.0.1：回环地址(Loop Back Address)，表达的意思其实是我们主机自己发出去的包，直接被自己接收;

✓ 正常的数据库包经常 应用层 - 传输层 - 网络层 - 数据链路层 - 物理层 ; 

✓ 而回环地址，是在网络层直接就被获取到了，是不会经常数据链路层和物理层的; 

✓ 比如我们监听 127.0.0.1时，在同一个网段下的主机中，通过ip地址是不能访问的;

0.0.0.0：监听IPV4上所有的地址，再根据端口找到不同的应用程序; 

✓ 比如我们监听 0.0.0.0时，在同一个网段下的主机中，通过ip地址是可以访问的;

#### port、open、compress

**port设置监听的端口，默认情况下是8080**

**open是否打开浏览器：**

默认值是false，设置为true会打开浏览器；

也可以设置为类似于 Google Chrome等值；

**compress是否为静态文件开启gzip compression：**

默认值是false，可以设置为true；

#### Proxy代理

**proxy是我们开发中非常常用的一个配置选项，它的目的设置代理来解决跨域访问的问题：**

比如我们的一个api请求是 http://localhost:8888，但是本地启动服务器的域名是 http://localhost:8080，这个时候发送网络请求就会出现跨域的问题；

那么我们可以将请求先发送到一个代理服务器，代理服务器和API服务器没有跨域的问题，就可以解决我们的跨域问题了；

**我们可以进行如下的设置：**

target：表示的是代理到的目标地址，比如 /api-hy/moment会被代理到 http://localhost:8888/api-hy/moment； 

pathRewrite：默认情况下，我们的 /api-hy 也会被写入到URL中，如果希望删除，可以使用pathRewrite； 

changeOrigin：它表示是否更新代理后请求的headers中host地址；

#### changeOrigin

**其实是要修改代理请求中的headers中的host属性：**

因为我们真实的请求，其实是需要通过 http://localhost:8888来请求的；

但是因为使用了代码，默认情况下它的值时 http://localhost:8000； 

如果我们需要修改，那么可以将changeOrigin设置为true即可；

#### historyApiFallback

**historyApiFallback是开发中一个非常常见的属性，它主要的作用是解决SPA页面在路由跳转之后，进行页面刷新时，返回404**

**的错误。**

**boolean值：默认是false**

如果设置为true，那么在刷新时，返回404错误时，会自动返回 index.html 的内容；

**object类型的值，可以配置rewrites属性**

可以配置from来匹配路径，决定要跳转到哪一个页面；

**事实上devServer中实现historyApiFallback功能是通过connect-history-api-fallback库的**：

## 如何使用webpack性能优化？

**比较常见的面试题包括：**

可以配置哪些属性来进行webpack性能优化？ 

前端有哪些常见的性能优化？（问到前端性能优化时，除了其他常见的，也完全可以从webpack来回答）

**webpack的性能优化较多，我们可以对其进行分类：**

优化一：打包后的结果，上线时的性能优化。（比如分包处理、减小包体积、CDN服务器等）

优化二：优化打包速度，开发或者构建时优化打包速度。（比如exclude、cache-loader等）

**大多数情况下，我们会更加侧重于优化一，这对于线上的产品影响更大**

**在大多数情况下webpack都帮我们做好了该有的性能优化：**

比如配置mode为production或者development时，默认webpack的配置信息；

但是我们也可以针对性的进行自己的项目优化；

### 性能优化 - 代码分离

**代码分离（Code Splitting）是webpack一个非常重要的特性**

它主要的目的是将代码分离到不同的bundle中，之后我们可以按需加载，或者并行加载这些文件； 

比如默认情况下，所有的JavaScript代码（业务代码、第三方依赖、暂时没有用到的模块）在首页全部都加载，就会影响首页的加载速度；

代码分离可以分出更小的bundle，以及控制资源加载优先级，提供代码的加载性能；

**Webpack中常用的代码分离有三种：**

入口起点：使用entry配置手动分离代码；

防止重复：使用Entry Dependencies或者SplitChunksPlugin去重和分离代码；

动态导入：通过模块的内联函数调用来分离代码；

#### 多入口起点

将入口文件分为多个，将必要的库，但是又不大会改变的代码打成一个包，例如jquery，图片，bootstrap等，多入口都依赖的文件可以使用dependOn属性，下面的shared属性是自定义属性，这样shared会自己打成一个包，而不会打包到index，main入口文件，减少包的体积，index，main共享这个包

```js
entry: {
    index: {
      import: './src/index.js',
      dependOn: 'shared'
    },
    main: {
      import: './src/main.js',
      dependOn: 'shared'
    },
    shared: ['axios']
  },
  output: {
    path: path.resolve(__dirname, './build'),
    // placeholder
    filename: '[name]-bundle.js',
    clean: true
  },
```

![](https://raw.githubusercontent.com/yangzeng-cell/blogImage3/main/%E6%88%AA%E5%B1%8F2023-06-05%2022.30.31.png)

上图就是打包之后的样子

#### 动态导入(dynamic import)

**另外一个代码拆分的方式是动态导入时，webpack提供了两种实现动态导入的方式：**

第一种，使用ECMAScript中的 import() 语法来完成，也是目前推荐的方式；

第二种，使用webpack遗留的 require.ensure，目前已经不推荐使用；

**比如我们有一个模块 bar.js：** 

该模块我们希望在代码运行过程中来加载它（比如判断一个条件成立时加载）；

因为我们并不确定这个模块中的代码一定会用到，所以最好拆分成一个独立的js文件； 

这样可以保证不用到该内容时，浏览器不需要加载和处理该文件的js代码； 

这个时候我们就可以使用动态导入； 

**注意：使用动态导入bar.js：** 

在webpack中，通过动态导入获取到一个对象； 

真正导出的内容，在该对象的default属性中，所以我们需要做一个简单的解构；

```
/**
 * import的内容刚开始是不会下载下来的，用到的时候才会下载下来
 */
btn1.onclick = function () {
  //魔法注释，修改到处bundle.js的命名,不一定需要配合webpack的chunkFileName一起使用
  import(/* webpackChunkName: "about" */ "./router/about").then((res) => {
    res.about(); //如果是使用export导出的
    res.default(); //如果是使用export default 导出的
  });
};

btn2.onclick = function () {
  import(/* webpackChunkName: "category" */ "./router/category");
};
```

#### 动态导入的文件命名

**动态导入的文件命名：**

因为动态导入通常是一定会打包成独立的文件的，所以并不会再cacheGroups中进行配置；

那么它的命名我们通常会在output中，通过 chunkFilename 属性来命名；

```js
output: {
    clean: true,
    path: path.resolve(__dirname, "./build"),
    // placeholder
    filename: "[name]-bundle.js",
    // 单独针对分包的文件进行命名
    chunkFilename: "[id]_[name]_chunk.js",
  },
```

通过chunkFilename来定义文件的文字,默认情况下，id和name的名字是一样的

如果我们希望修改name的值，可以通过magic comments（魔法注释）的方式；

```js
import(/* webpackChunkName: "category" */ "./router/category");
```

#### optimization.chunkIds配置

optimization.chunkIds配置用于告知webpack模块的id采用什么算法生成。

**有三个比较常见的值：**

natural：按照数字的顺序使用id； 

named：development下的默认值，一个可读的名称的id； 

deterministic：确定性的，在不同的编译中不变的短数字id

✓ 在webpack4中是没有这个值的；

✓ 那个时候如果使用natural，那么在一些编译发生变化时，就会有问题；

**最佳实践：**

开发过程中，我们推荐使用named； 

打包过程中，我们推荐使用deterministic；

#### optimization.runtimeChunk配置

**配置runtime相关的代码是否抽取到一个单独的chunk中：**

runtime相关的代码指的是在运行环境中，对模块进行解析、加载、模块信息相关的代码； 

比如我们的component、bar两个通过import函数相关的代码加载，就是通过runtime代码完成的；

**抽离出来后，有利于浏览器缓存的策略：**

比如我们修改了业务代码（main），那么runtime和component、bar的chunk是不需要重新加载的；

比如我们修改了component、bar的代码，那么main中的代码是不需要重新加载的；

设置的值：

true/multiple：针对每个入口打包一个runtime文件；

single：打包一个runtime文件；

对象：name属性决定runtimeChunk的名称；

```js
  // 优化配置
  optimization: {
    // 设置生成的chunkId的算法
    // development: named
    // production: deterministic(确定性)
    // webpack4中使用: natural
    chunkIds: "deterministic",
    // runtime的代码是否抽取到单独的包中(早Vue2脚手架中)
    runtimeChunk: {
      name: "runtime",
    },
    // 分包插件: SplitChunksPlugin
    splitChunks: {
      /**
       * 默认情况下只对异步代码进行分饱
       */
      chunks: "all", //initial 同步代码分包，async  异步代码分包，同步异步投分饱
      // 当一个包大于指定的大小时, 继续进行拆包，但是必须保持包的完整性
      // maxSize: 20000,
      // // 将包拆分成不小于minSize的包
      // minSize: 10000,
      minSize: 10,

      // 自己对需要进行拆包的内容进行分包
      cacheGroups: {
        utils: {
          test: /utils/, //utils目录下的文件进行分饱
          filename: "[id]_utils.js",
        },
        vendors: {
          // /node_modules/
          // window上面 /\
          // mac上面 /
          test: /[\\/]node_modules[\\/]/, //所有node__module 目录下的进行分包
          filename: "[id]_vendors.js",
        },
      },
    },
    // 代码优化: TerserPlugin => 让代码更加简单 => Terser
    minimizer: [
      // JS代码简化
      new TerserPlugin({
        //这个插件webpack已经集成，
        extractComments: false, //可以用于删除提取的注视文件
      }),
      // CSS代码简化
    ],
  },
```

#### Prefetch和Preload

webpack v4.6.0+ 增加了对预获取和预加载的支持。

在声明 import 时，使用下面这些内置指令，来告知浏览器

**prefetch**(预获取)：将来某些导航下可能需要的资源

**preload**(预加载)：当前导航下可能需要资源

**与 prefetch 指令相比，preload 指令有许多不同之处：**

preload chunk 会在父 chunk 加载时，以并行方式开始加载。prefetch chunk 会在父 chunk 加载结束后开始加载。

preload chunk 具有中等优先级，并立即下载。prefetch chunk 在浏览器闲置时下载。

preload chunk 会在父 chunk 中立即请求，用于当下时刻。prefetch chunk 会用于未来的某个时刻。

```js
btn1.onclick = function() {
  import(
    /* webpackChunkName: "about" */
    /* webpackPrefetch: true */
    './router/about').then(res => {
    res.about()
    res.default()
  })
}

btn2.onclick = function() {
  import(
    /* webpackChunkName: "category" */
    /* webpackPrefetch: true */
    './router/category')
}
```

#### CDN

在开发中，我们使用CDN主要是两种方式：

方式一：打包的所有静态资源，放到CDN服务器，

用户所有资源都是通过CDN服务器加载的；

方式二：一些第三方资源放到CDN服务器上；



在开发环境中可以使用node_module中加载，在生产环境中使用cdn加载

```js
output: {
    clean: true,
    path: path.resolve(__dirname, "./build"),
    // placeholder
    filename: "[name]-bundle.js",
    // 单独针对分包的文件进行命名
    chunkFilename: "[name]_chunk.js",
    publicPath: "http://coderwhycdn.com/",
  },
  // 排除某些包不需要进行打包
  externals: {
    react: "React",
    // key属性名: 排除的框架的名称
    // value值: 从CDN地址请求下来的js中提供对应的名称
    axios: "axios",
  },
```

在publicPath中配置cdn的路径，打包之后就会自动加载这个cdn地址

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width,initial-scale=1" />
    <title>Document</title>
    <script
      defer="defer"
      src="http://coderwhycdn.com/runtime-bundle.js"
    ></script>
    <script defer="defer" src="http://coderwhycdn.com/497_utils.js"></script>
    <script defer="defer" src="http://coderwhycdn.com/main-bundle.js"></script>
  </head>
  <body>
    <div id="root"></div>
    <script src="https://cdn.bootcdn.net/ajax/libs/axios/1.2.0/axios.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/react/18.2.0/umd/react.production.min.js"></script>
  </body>
</html>
```

第三方的库可以使用cdn的方式进行引入，可以在webpack配置中来排出这些包，在html模版文件中使用cdn的地址

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <meta http-equiv="X-UA-Compatible" content="IE=edge" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Document</title>
  </head>
  <body>
    <div id="root"></div>
    <!-- 排除的包，在这里手动加载这个cdn包 -->
    <script src="https://cdn.bootcdn.net/ajax/libs/axios/1.2.0/axios.min.js"></script>
    <script src="https://cdn.bootcdn.net/ajax/libs/react/18.2.0/umd/react.production.min.js"></script>
  </body>
</html>
```

#### MiniCssExtractPlugin

**MiniCssExtractPlugin可以帮助我们将css提取到一个独立的css文件中，该插件需要在webpack4+才可以使用。**

**首先，我们需要安装 mini-css-extract-plugin：**

```
npm install mini-css-extract-plugin -D
```

 **配置rules和plugins：**

```js
 module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          // 'style-loader', 开发阶段
          MiniCssExtractPlugin.loader, // 生产阶段
          "css-loader",
        ],
      },
    ],
  },
  plugins: [
    // 完成css的提取
    new MiniCssExtractPlugin({
      filename: "css/[name].css",
      chunkFilename: "css/[name]_chunk.css",
    }),
  ],
```



```js
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const TerserPlugin = require("terser-webpack-plugin");
const { ProvidePlugin } = require("webpack");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const CSSMinimizerPlugin = require("css-minimizer-webpack-plugin");

module.exports = {
  mode: "production",
  devtool: false,
  // entry: './src/index.js',
  entry: "./src/main.js",
  output: {
    clean: true,
    path: path.resolve(__dirname, "./build"),
    // placeholder
    filename: "js/[name]-bundle.js",
    // 单独针对分包的文件进行命名
    chunkFilename: "js/[name]_chunk.js",
    // publicPath: 'http://coderwhycdn.com/'
  },
  // 优化配置
  optimization: {
    // 设置生成的chunkId的算法
    // development: named
    // production: deterministic(确定性)
    // webpack4中使用: natural
    chunkIds: "deterministic",
    // runtime的代码是否抽取到单独的包中(早Vue2脚手架中)
    runtimeChunk: {
      name: "runtime",
    },
    // 分包插件: SplitChunksPlugin
    splitChunks: {
      chunks: "all",
      // 当一个包大于指定的大小时, 继续进行拆包
      // maxSize: 20000,
      // // 将包拆分成不小于minSize的包
      // minSize: 10000,
      minSize: 10,

      // 自己对需要进行拆包的内容进行分包
      cacheGroups: {
        utils: {
          test: /utils/,
          filename: "js/[id]_utils.js",
        },
        vendors: {
          // /node_modules/
          // window上面 /\
          // mac上面 /
          test: /[\\/]node_modules[\\/]/,
          filename: "js/[id]_vendors.js",
        },
      },
    },
    //为了minimizer生效。需要设置minimize为true，production模式默认会设置为true
    minimize: true,
    // 代码优化: TerserPlugin => 让代码更加简单 => Terser
    minimizer: [
      // JS压缩的插件: TerserPlugin
      new TerserPlugin({
        extractComments: false,
        //配置
        terserOptions: {
          //压缩
          compress: {
            arguments: true,
            unused: true,
          },
          //删除用不到的代码
          mangle: true,
          // toplevel: false
          keep_fnames: true,
        },
      }),
      // CSS压缩的插件: CSSMinimizerPlugin
      new CSSMinimizerPlugin({
        // parallel: true
      }),
    ],
  },
  module: {
    rules: [
      {
        test: /\.jsx?$/,
        use: {
          loader: "babel-loader",
        },
      },
      {
        test: /\.ts$/,
        use: "babel-loader",
      },
      {
        test: /\.css$/,
        use: [
          // 'style-loader', 开发阶段
          MiniCssExtractPlugin.loader, // 生产阶段
          "css-loader",
        ],
      },
    ],
  },
  plugins: [
    new HtmlWebpackPlugin({
      template: "./index.html",
    }),
    new ProvidePlugin({
      axios: ["axios", "default"],
      // get: ['axios', 'get'],
      dayjs: "dayjs",
    }),
    // 完成css的提取
    new MiniCssExtractPlugin({
      filename: "css/[name].css",
      chunkFilename: "css/[name]_chunk.css",
    }),
  ],
};

```



### Hash、ContentHash、ChunkHash

**在我们给打包的文件进行命名的时候，会使用placeholder，placeholder中有几个属性比较相似：**

hash、chunkhash、contenthash

hash本身是通过MD4的散列函数处理后，生成一个128位的hash值（32个十六进制）；

**hash值的生成和整个项目有关系：**

比如我们现在有两个入口index.js和main.js； 

它们分别会输出到不同的bundle文件中，并且在文件名称中我们有使用hash； 

这个时候，如果修改了index.js文件中的内容，那么hash会发生变化；

那就意味着两个文件的名称都会发生变化；

**chunkhash可以有效的解决上面的问题，它会根据不同的入口进行借来解析来生成hash值：**

比如我们修改了index.js，那么main.js的chunkhash是不会发生改变的；

**contenthash表示生成的文件hash名称，只和内容有关系：**

比如我们的index.js，引入了一个style.css，style.css有被抽取到一个独立的css文件中；

这个css文件在命名时，如果我们使用的是chunkhash； 

那么当index.js文件的内容发生变化时，css文件的命名也会发生变化；

这个时候我们可以使用contenthash；

```js
const path = require('path')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')

module.exports = {
  mode: 'development',
  entry: {
    index: './src/index.js',
    main: './src/main.js',
  },
  output: {
    clean: true,
    path: path.resolve(__dirname, './build'),
    filename: '[name]_[contenthash]_bundle.js',
    chunkFilename: '[contenthash]_chunk.js'
  },
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader'
        ]
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[contenthash]_[name].css'
    })
  ]
}
```

### Terser介绍和安装

**什么是Terser呢？**

Terser是一个JavaScript的解释（Parser）、Mangler（绞肉机）/Compressor（压缩机）的工具集；

早期我们会使用 uglify-js来压缩、丑化我们的JavaScript代码，但是目前已经不再维护，并且不支持ES6+的语法；

Terser是从 uglify-es fork 过来的，并且保留它原来的大部分API以及适配 uglify-es和uglify-js@3等；

**也就是说，Terser可以帮助我们压缩、丑化我们的代码，让我们的bundle变得更小**

**因为Terser是一个独立的工具，所以它可以单独安装：**

```
# 全局安装
npm install terser -g
# 局部安装
npm install terser -D
```

**命令行使用Terser**

```
terser [input files] [options]
# 举例说明
terser js/file1.js -o foo.min.js -c -m
```

**Compress和Mangle的options**

**Compress option：** 

arrows：class或者object中的函数，转换成箭头函数；

arguments：将函数中使用 arguments[index]转成对应的形参名称；

dead_code：移除不可达的代码（tree shaking）；

其他属性可以查看文档；

**Mangle option**

toplevel：默认值是false，顶层作用域中的变量名称，进行丑化（转换）；

keep_classnames：默认值是false，是否保持依赖的类名称；

keep_fnames：默认值是false，是否保持原来的函数名称；

其他属性可以查看文档；

```
npx terser ./src/abc.js -o abc.min.js -c 
arrows,arguments=true,dead_code -m 
toplevel=true,keep_classnames=true,keep_fnames=true
```

#### Terser在webpack中配置

**真实开发中，我们不需要手动的通过terser来处理我们的代码，我们可以直接通过webpack来处理：**

在webpack中有一个minimizer属性，在production模式下，默认就是使用TerserPlugin来处理我们的代码的；

如果我们对默认的配置不满意，也可以自己来创建TerserPlugin的实例，并且覆盖相关的配置；

**首先，我们需要打开minimize，让其对我们的代码进行压缩（默认production模式下已经打开了）**

**其次，我们可以在minimizer创建一个TerserPlugin：** 

extractComments：默认值为true，表示会将注释抽取到一个单独的文件中；

✓ 在开发中，我们不希望保留这个注释时，可以设置为false； 

 parallel：使用多进程并发运行提高构建的速度，默认值是true

✓ 并发运行的默认数量： os.cpus().length - 1； 

✓ 我们也可以设置自己的个数，但是使用默认值即可；

 terserOptions：设置我们的terser相关的配置

✓ compress：设置压缩相关的选项；

✓ mangle：设置丑化相关的选项，可以直接设置为true； 

✓ toplevel：顶层变量是否进行转换；

✓ keep_classnames：保留类的名称；

✓ keep_fnames：保留函数的名称；

```js
 // 优化配置
  optimization: {
    //为了minimizer生效。需要设置minimize为true，production模式默认会设置为true
    minimize: true,
    // 代码优化: TerserPlugin => 让代码更加简单 => Terser
    minimizer: [
      // JS压缩的插件: TerserPlugin
      new TerserPlugin({
        extractComments: false,
        //配置
        terserOptions: {
          //压缩
          compress: {
            arguments: true,
            unused: true,
          },
          //删除用不到的代码
          mangle: true,
          // toplevel: false
          keep_fnames: true,
        },
      }),
    ],
  },
```

### CSS的压缩

**另一个代码的压缩是CSS：** 

CSS压缩通常是去除无用的空格等，因为很难去修改选择器、属性的名称、值等；

CSS的压缩我们可以使用另外一个插件：css-minimizer-webpack-plugin； 

css-minimizer-webpack-plugin是使用cssnano工具来优化、压缩CSS（也可以单独使用）；

**第一步，安装 css-minimizer-webpack-plugin：**

```
npm install css-minimizer-webpack-plugin -D
```

**第二步，在optimization.minimizer中配置**

```
 // CSS压缩的插件: CSSMinimizerPlugin
      new CSSMinimizerPlugin({
        // parallel: true
      }),
```

### 什么是Tree Shaking

**什么是Tree Shaking呢？**

 Tree Shaking是一个术语，在计算机中表示消除死代码（dead_code）；

 最早的想法起源于LISP，用于消除未调用的代码（纯函数无副作用，可以放心的消除，这也是为什么要求我们在进行函数式

编程时，尽量使用纯函数的原因之一）；

 后来Tree Shaking也被应用于其他的语言，比如JavaScript、Dart； 

◼ **JavaScript的Tree Shaking：** 

 对JavaScript进行Tree Shaking是源自打包工具rollup（后面我们也会讲的构建工具）；

 这是因为Tree Shaking依赖于ES Module的静态语法分析（不执行任何的代码，可以明确知道模块的依赖关系）；

 webpack2正式内置支持了ES2015模块，和检测未使用模块的能力；

 在webpack4正式扩展了这个能力，并且通过 package.json的 sideEffects属性作为标记，告知webpack在编译时，哪里文

件可以安全的删除掉；

 webpack5中，也提供了对部分CommonJS的tree shaking的支持；

✓ https://github.com/webpack/changelog-v5#commonjs-tree-shaking

**webpack实现Tree Shaking**

**事实上webpack实现Tree Shaking采用了两种不同的方案：**

 usedExports：通过标记某些函数是否被使用，之后通过Terser来进行优化的；

 sideEffects：跳过整个模块/文件，直接查看该文件是否有副作用；

**usedExports**

**将mode设置为development模式：**

 为了可以看到 usedExports带来的效果，我们需要设置为 development 模式

 因为在 production 模式下，webpack默认的一些优化会带来很大的影响。

◼ **设置usedExports为true和false对比打包后的代码：**

 在usedExports设置为true时，会有一段注释：unused harmony export mul； 

 这段注释的意义是什么呢？告知Terser在优化时，可以删除掉这段代码；

◼ **这个时候，我们讲 minimize设置true：** 

 usedExports设置为false时，mul函数没有被移除掉；

 usedExports设置为true时，mul函数有被移除掉；

◼ **所以，usedExports实现Tree Shaking是结合Terser来完成的。**

```js
// 优化配置
  optimization: {
    // 导入模块时, 分析模块中的哪些函数有被使用, 哪些函数没有被使用.
    usedExports: true,

    chunkIds: "deterministic",
    // runtime的代码是否抽取到单独的包中(早Vue2脚手架中)
    runtimeChunk: {
      name: "runtime",
    },
    // 分包插件: SplitChunksPlugin
    splitChunks: {
      chunks: "all",
      minSize: 10,

      // 自己对需要进行拆包的内容进行分包
      cacheGroups: {
        utils: {
          test: /utils/,
          filename: "js/[id]_utils.js",
        },
        vendors: {
          // /node_modules/
          // window上面 /\
          // mac上面 /
          test: /[\\/]node_modules[\\/]/,
          filename: "js/[id]_vendors.js",
        },
      },
    },
    minimize: true,
    // 代码优化: TerserPlugin => 让代码更加简单 => Terser
    minimizer: [
      // JS压缩的插件: TerserPlugin
      new TerserPlugin({
        extractComments: false,
        terserOptions: {
          compress: {
            arguments: true,
            unused: true,
          },
          mangle: true,
          // toplevel: false
          keep_fnames: true,
        },
      }),
      // CSS压缩的插件: CSSMinimizerPlugin
      new CSSMinimizerPlugin({
        // parallel: true
      }),
    ],
  },
```

#### sideEffects

**sideEffects用于告知webpack compiler哪些模块时有副作用的：**

 副作用的意思是这里面的代码有执行一些特殊的任务，不能仅仅通过export来判断这段代码的意义；

 副作用的问题，在讲React的纯函数时是有讲过的；

◼ **在package.json中设置sideEffects的值：**

 如果我们将sideEffects设置为false，就是告知webpack可以安全的删除未用到的exports； 

 如果有一些我们希望保留，可以设置为数组；

◼ **比如我们有一个format.js、style.css文件：**

 该文件在导入时没有使用任何的变量来接受；

 那么打包后的文件，不会保留format.js、style.css相关的任何代码；

```json
//package.json
“sideEffects”:[
	"./src/util/format.js",
	"*.css"
]
```



#### Webpack中tree shaking的设置

**所以，如何在项目中对JavaScript的代码进行TreeShaking呢（生成环境）？**

 在optimization中配置usedExports为true，来帮助Terser进行优化；

 在package.json中配置sideEffects，直接对模块进行优化；

#### CSS实现Tree Shaking

Tree Shaking：PurgeCSS，也是一个帮助我们删除未使用的CSS的工具；

```
npm install purgecss-webpack-plugin -D
```

#### 配置PurgeCss

**配置这个插件（生成环境）：**

paths：表示要检测哪些目录下的内容需要被分析，这里我们可以使用glob； 

默认情况下，Purgecss会将我们的html标签的样式移除掉，如果我们希望保留，可以添加一个safelist的属性；

```js
new PurgeCSSPlugin({
      paths: glob.sync(`${path.resolve(__dirname, "../src")}/**/*`, {
        nodir: true,
      }),
      safelist: function () {
        return {
          standard: ["body"],//排除掉这个标签
        };
      },
    }),
```

**purgecss也可以对less文件进行处理（所以它是对打包后的css进行tree shaking操作）；**



#### Scope Hoisting

**什么是Scope Hoisting呢？**

Scope Hoisting从webpack3开始增加的一个新功能；

功能是对作用域进行提升，并且让webpack打包后的代码更小、运行更快；

◼ **默认情况下webpack打包会有很多的函数作用域，包括一些（比如最外层的）IIFE：** 

 无论是从最开始的代码运行，还是加载一个模块，都需要执行一系列的函数；

 Scope Hoisting可以将函数合并到一个模块中来运行；

◼ **使用Scope Hoisting非常的简单，webpack已经内置了对应的模块：**

 在production模式下，默认这个模块就会启用；

 在development模式下，我们需要自己来打开该模块；

```js
 // 作用域提升
    new webpack.optimize.ModuleConcatenationPlugin()
```

### HTTP压缩

```js
// 对打包后的文件(js/css)进行压缩
    new CompressionPlugin({
      test: /\.(js|css)$/,
      algorithm: 'gzip'
    })
```

**目前的压缩格式非常的多：**

compress – UNIX的“compress”程序的方法（历史性原因，不推荐大多数应用使用，应该使用gzip或deflate）；

deflate – 基于deflate算法（定义于RFC 1951）的压缩，使用zlib数据格式封装；

gzip – GNU zip格式（定义于RFC 1952），是目前使用比较广泛的压缩算法；

br – 一种新的开源压缩算法，专为HTTP内容的编码而设计；



#### HTML文件中代码的压缩

```js
 new HtmlWebpackPlugin({
        template: "./index.html",
        cache: true,
        //压缩html
        minify: isProdution
          ? {
              // 移除注释
              removeComments: true,
              // 移除属性
              removeEmptyAttributes: true,
              // 移除默认属性
              removeRedundantAttributes: true,
              // 折叠空白字符
              collapseWhitespace: true,
              // 压缩内联的CSS
              minifyCSS: true,
              // 压缩JavaScript
              minifyJS: {
                mangle: {
                  toplevel: true,
                },
              },
            }
          : false,
      }),
```

## 常见的webpack配置

```js
//comm.config.js 将development和production的公共配置抽离出来
const path = require("path");
const HtmlWebpackPlugin = require("html-webpack-plugin");
const { ProvidePlugin } = require("webpack");
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
const { merge } = require("webpack-merge");
const devConfig = require("./dev.config");
const prodConfig = require("./prod.config");

const getCommonConfig = function (isProdution) {
  return {
    entry: "./src/demo.js",
    output: {
      clean: true,
      path: path.resolve(__dirname, "../build"),
      // placeholder
      filename: "js/[name]-bundle.js",
      // 单独针对分包的文件进行命名
      chunkFilename: "js/[name]_chunk.js",
      // publicPath: 'http://coderwhycdn.com/'
    },
    resolve: {
      extensions: [".js", ".json", ".wasm", ".jsx", ".ts"],
    },
    module: {
      rules: [
        {
          test: /\.jsx?$/,
          use: {
            loader: "babel-loader",
          },
        },
        {
          test: /\.ts$/,
          use: "babel-loader",
        },
        {
          test: /\.css$/,
          use: [
            // // 'style-loader', //开发阶段
            // MiniCssExtractPlugin.loader, // 生产阶段
            isProdution ? MiniCssExtractPlugin.loader : "style-loader",
            "css-loader",
          ],
        },
      ],
    },
    plugins: [
      new HtmlWebpackPlugin({
        template: "./index.html",
        cache: true,
        //压缩html
        minify: isProdution
          ? {
              // 移除注释
              removeComments: true,
              // 移除属性
              removeEmptyAttributes: true,
              // 移除默认属性
              removeRedundantAttributes: true,
              // 折叠空白字符
              collapseWhitespace: true,
              // 压缩内联的CSS
              minifyCSS: true,
              // 压缩JavaScript
              minifyJS: {
                mangle: {
                  toplevel: true,
                },
              },
            }
          : false,
      }),
      new ProvidePlugin({
        axios: ["axios", "default"],
        // get: ['axios', 'get'],
        dayjs: "dayjs",
      }),
    ],
  };
};

// webpack允许导出一个函数
module.exports = function (env) {
  const isProduction = env.production;
  let mergeConfig = isProduction ? prodConfig : devConfig;
  return merge(getCommonConfig(isProduction), mergeConfig);
};
```



//dev.config.js

```js
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const TerserPlugin = require('terser-webpack-plugin')
const { ProvidePlugin } = require('webpack')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const CSSMinimizerPlugin = require('css-minimizer-webpack-plugin')

module.exports = {
  mode: 'development',
  devServer: {
    static: ['public', 'content'],
    port: 3000,
    compress: true,
    proxy: {
      '/api': {
        target: 'http://localhost:9000',
        pathRewrite: {
          '^/api': ''
        },
        changeOrigin: true
      }
    },
    historyApiFallback: true
  },
  plugins: [
  ]
}
```

//prod.config.js

```js
const path = require('path')
const glob = require('glob')
const webpack = require('webpack')

const TerserPlugin = require('terser-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const CSSMinimizerPlugin = require('css-minimizer-webpack-plugin')
const { PurgeCSSPlugin } = require('purgecss-webpack-plugin')
const CompressionPlugin = require("compression-webpack-plugin")

console.log(glob.sync(`${path.resolve(__dirname, './src')}/**/*`, { nodir: true }))


module.exports = {
  mode: 'production',
  devtool: false,
  // 优化配置
  optimization: {
    // 导入模块时, 分析模块中的哪些函数有被使用, 哪些函数没有被使用.
    usedExports: true,
    
    chunkIds: 'deterministic',
    // runtime的代码是否抽取到单独的包中(早Vue2脚手架中)
    runtimeChunk: {
      name: "runtime"
    },
    // 分包插件: SplitChunksPlugin
    splitChunks: {
      chunks: "all",
      minSize: 10,

      // 自己对需要进行拆包的内容进行分包
      cacheGroups: {
        utils: {
          test: /utils/,
          filename: "js/[id]_utils.js"
        },
        vendors: {
          // /node_modules/
          // window上面 /\
          // mac上面 /
          test: /[\\/]node_modules[\\/]/,
          filename: "js/[id]_vendors.js"
        }
      }
    },
    minimize: true,
    // 代码优化: TerserPlugin => 让代码更加简单 => Terser
    minimizer: [
      // JS压缩的插件: TerserPlugin
      new TerserPlugin({
        extractComments: false,
        terserOptions: {
          compress: {
            arguments: true,
            unused: true
          },
          mangle: true,
          // toplevel: false
          keep_fnames: true
        }
      }),
      // CSS压缩的插件: CSSMinimizerPlugin
      // new CSSMinimizerPlugin({
      //   // parallel: true
      // })
    ],
  },
  plugins: [
    // 完成css的提取
    new MiniCssExtractPlugin({
      filename: 'css/[name].css',
      chunkFilename: 'css/[name]_chunk.css'
    }),
    // 对CSS进行TreeShaking
    // new PurgeCSSPlugin({
    //   paths: glob.sync(`${path.resolve(__dirname, '../src')}/**/*`, { nodir: true }),
    //   safelist: function() {
    //     return {
    //       standard: ["body"]
    //     }
    //   }
    // }),
    // 作用域提升
    new webpack.optimize.ModuleConcatenationPlugin(),
    // 对打包后的文件(js/css)进行压缩
    new CompressionPlugin({
      test: /\.(js|css)$/,
      algorithm: 'gzip'
    })
  ]
}
```



```json
"scripts": {
    "build": "webpack --config ./config/comm.config.js --env production",
    "serve": "webpack serve --config ./config/comm.config.js --env development",
    "ts-check": "tsc --noEmit",
    "ts-check-watch": "tsc --noEmit --watch"
  },
```

通过命令行来进行打包
