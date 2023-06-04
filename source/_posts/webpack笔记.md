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
