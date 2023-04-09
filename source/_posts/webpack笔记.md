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
