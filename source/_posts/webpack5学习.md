---
title: webpack5学习
date: 2022-09-08 16:50:36
tags:
categories:
- [常用构建工具,webpack]
---

```js
//path.js
const path = require('path');

const appDir = process.cwd();
console.log(`Current directory: ${appDir}`);

const resolveApp = (relativePath) => path.resolve(appDir, relativePath);

module.exports = resolveApp;
```

```js
//webpack.common.js
const resolveApp = require('./paths');
const HtmlWebpackPlugin = require('html-webpack-plugin');
const { DefinePlugin } = require('webpack');
const CopyWebpackPlugin = require('copy-webpack-plugin');
const ESLintPlugin = require('eslint-webpack-plugin');
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const { merge } = require('webpack-merge');

const productionConfig = require('./webpack.prod');
const developmentConfig = require('./webpack.dev');

/***
 * 这里有三种方法来进行代码分离，它可以减少打包文件大小，减少文件的加载时间
 *  它主要的目的是将代码分离到不同的bundle中，之后我们可以按需加载，或者并行加载这些文件；
    比如默认情况下，所有的JavaScript代码（业务代码、第三方依赖、暂时没有用到的模块）在首页全部都加载，
就会影响首页的加载速度
    代码分离可以分出出更小的bundle，以及控制资源加载优先级，提供代码的加载性能
      1.入口起点：使用entry配置手动分离代码；
      2.防止重复：使用Entry Dependencies或者SplitChunksPlugin去重和分离代码；
      3.动态导入：通过模块的内联函数调用来分离代码
 */
const commonConfig = (env, args) => {
  const devMode = args.mode !== 'production';
  return {
    entry: ['./src/index.js'],
    // 多入口文件 如果两个文件引用相同的代码会造成重复打包，增大单个bundle的大小，从而浪费带宽
    // entry: {
    //   index: './src/index.js',
    //   another: './src/another-module.js'
    // },
    //将公共的包分离出来，减小bundle体积
    // entry: {
    //   index: {
    //     import: './src/index.js',
    //     dependOn: 'shared'
    //   },
    //   another: {
    //     import: './src/another-module.js',
    //     dependOn: 'shared'
    //   },
    //   shared: 'lodash'
    // },
    output: {
      filename: 'js/[name].bundle.js',
      // 为import().then异步加载使用
      chunkFilename: 'js/[name].[hash:6].chunk.js',
      // 必须是一个绝对路径
      path: resolveApp('./dist'),
      // 重新打包时清理dist文件夹
      clean: true
      // 使用webpack-dev-middleware需要 目的是让文件在服务器中也可以使用,找到对应的文件,要在生产环境放到服务器中才加
      // output中的path的作用是告知webpack之后的输出目录
      // publicPath: "/",
      // Asset Modules 将这个模块下的文件输出到dist下的指定文件夹
      // assetModuleFilename: "img/[name].[hash:6][ext]"
    },
    // /如果我们要在单个 HTML 页面上使用多个入口点，还需要 optimization.runtimeChunk: 'single'
    // optimization: {
    //   runtimeChunk: 'single'
    // },
    optimization: {
      // optimization.chunkIds配置用于告知webpack模块的id采用什么算法生成
      // natural: 使用自然数(不推荐),
      // named: 使用包所在目录作为name(在开发环境推荐)
      // deterministic: 生成id, 针对相同文件生成的id是不变
      // chunkIds: devMode ? 'deterministic' : 'named',
      // 方式二使用splitChunkPlugin默认集成
      splitChunks: {
        /**
         * async 默认 针对异步导入
         * initial 针对同步导入
         * all 针对同步和异步
         */
        chunks: 'all',
        // 最小尺寸: 如果拆分出来一个, 那么拆分出来的这个包的大小最小为minSize
        minSize: 20000,
        // 将大于maxSize的包, 拆分成不小于minSize的包
        maxSize: 20000,
        // 表示至少被引入一次的包才会被分包，没有引用过的不会被打包
        minChunks: 1,
        /**
         * cacheGroups：
 用于对拆分的包就行分组，比如一个lodash在拆分之后，并不会立即打包，而是会等到有没有其他符合规则的包一起来打
包；
 test属性：匹配符合规则的包；
 name属性：拆分包的name属性；
 filename属性：拆分包的名称，可以自己使用placeholder属性
         */
        cacheGroups: {
          vendor: {
            test: /[\\/]node_modules[\\/]/,
            filename: 'js/[id]_venders.js',
            priority: -10
          },
          default: {
            minChunks: 2,
            filename: 'js/common_[id].js',
            priority: -20
          }
        }
      },
      /**optimization. runtimeChunk配置
       * 配置runtime相关的代码是否抽取到一个单独的chunk中：
       * runtime相关的代码指的是在运行环境中，对模块进行解析、加载、模块信息相关的代码；
       * 抽离出来后，有利于浏览器缓存的策略：
       * 比如我们修改了业务代码（main），那么runtime和component、bar的chunk是不需要重新加载的
       * 比如我们修改了component、bar的代码，那么main中的代码是不需要重新加载的
       *   true/multiple ：针对每个入口打包一个runtime文件；
             single 打包一个runtime文件
             object: name name属性决定runtimeChunk的名称；
       */
      runtimeChunk: {
        name: function (entrypoint) {
          return `other-${entrypoint.name}`;
        }
      }
    },
    module: {
      rules: [
        {
          test: /\.ts$/,
          exclude: /node_modules/,
          // 本质上是依赖于typescript(typescript compiler)
          use: 'babel-loader'
        },
        { test: /\.jsx$/i, exclude: /node_modules/, use: 'babel-loader' },
        {
          test: /\.m?js$/i,
          exclude: /node_modules/,
          // 将es6=》es5
          use: {
            loader: 'babel-loader'
          }
        },
        {
          test: /\.css$/i,
          use: [
            devMode ? 'style-loader' : MiniCssExtractPlugin.loader,
            {
              loader: 'css-loader',
              options: {
                // 允许在使用css-loader之前加载多少个模块 postcss-loader
                importLoaders: 1
              }
            },
            'postcss-loader'
          ]
        },
        {
          test: /\.less$/i,
          use: [
            devMode ? 'style-loader' : MiniCssExtractPlugin.loader,
            {
              loader: 'css-loader',
              options: {
                // 0 => no loaders (default);
                // 1 => postcss-loader;
                // 2 => postcss-loader, less-loader
                importLoaders: 2
              }
            },
            'postcss-loader',
            'less-loader'
          ]
        },
        /**
         * 资源模块类型(asset module type)，通过添加 4 种新的模块类型，来替换所有这些 loader：
         * 1.asset/resource 发送一个单独的文件并导出 URL。之前通过使用 file-loader 实现
         * 2.asset/inline 导出一个资源的 data URI。之前通过使用 url-loader 实现
         * 3.asset/source 导出资源的源代码。之前通过使用 raw-loader 实现
         * 4.asset 在导出一个 data URI 和发送一个单独的文件之间自动选择。之前通过使用 url-loader，并且配置资源
  体积限制实现
         */
        {
          test: /\.(png|jpeg|svg|gif|jpg)$/,
          type: 'asset',
          generator: {
            filename: 'img/[name].[hash:6].[ext]'
          },
          parser: {
            dataUrlCondition: {
              maxSize: 100 * 1024
            }
          }
        },
        {
          test: /\.(woff|woff2|eot|ttf|otf)$/i,
          type: 'asset/resource',
          generator: {
            filename: 'font/[name].[hash:6].[ext]'
          }
        },
        {
          test: /\.(csv|tsv)$/,
          use: ['csv-loader']
        },
        {
          test: /\.xml$/,
          use: ['xml-loader']
        }
      ]
    },
    resolve: {
      extensions: ['.wasm', '.mjs', '.js', '.json', '.jsx', '.ts', '.vue'],
      alias: {
        '@': resolveApp('./src')
      }
    },
    plugins: [
      new ESLintPlugin(),
      new HtmlWebpackPlugin({
        title: 'developement',
        // html模板
        template: './src/asset/index.html'
      }),
      // 定义全局常量
      new DefinePlugin({
        BASE_URL: '"./"'
      }),
      // 复制文件到dist文件夹
      new CopyWebpackPlugin({
        patterns: [
          {
            from: 'src/asset',
            globOptions: {
              ignore: ['**/index.html', '**/.DS_Store']
            }
          }
        ]
      })
    ]
  };
};

module.exports = (env, args) => {
  switch (args.mode) {
    case 'development':
      return merge(commonConfig(env, args), developmentConfig);
    case 'production':
      return merge(commonConfig(env, args), productionConfig);
    default:
      throw new Error('No matching configuration was fou)nd!');
  }
};
```

```js
//webpack.dev.js
const { HotModuleReplacementPlugin } = require('webpack');

module.exports = {
  entry: ['webpack-hot-middleware/client?noInfo=false&reload=true'],
  mode: 'development',
  // 在开发环境中使用
  devtool: 'source-map',
  // --watch 需要手动刷新页面不方便
  // 开启本地服务器
  // 官方其实有提到，建议 devServer.publicPath 与 output.publicPath相同
  devServer: {
    static: './dist',
    /**
     *  hot: 'only'：表示热模块替换，编译失败后不会刷新整个页面
     */
    hot: true,
    compress: true, //开启gzip压缩
    port: 'auto', //修改本地服务器端口号, auto由服务器自动分配一个端口号
    /**
     * historyApiFallback是开发中一个非常常见的属性，它主要的作用是解决SPA页面在路由跳转之后，进行页面刷新
时，返回404的错误
    如果设置为true，那么在刷新时，返回404错误时，会自动返回 index.html 的内容
     */
    historyApiFallback: true,
    //  设置主机地址
    // host: '0.0.0.0',
    // 是否自动打开浏览器
    // open: true,
    /**
     * 设置开发代理
     */
    proxy: {
      '/api': {
        target: 'http://localhost:3000/api',
        pathRewrite: { '^/api': '' },
        changeOrigin: true
      }
    }
  },
  plugins: [
    // 使用webpack-hot-middleware替代webpack-dev-middleware开启热更新，因为webpack-dev-middleware不能够热更新
    new HotModuleReplacementPlugin()
  ]
};
```

```js
//webpack.prod.js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
const CssMinimizerPlugin = require('css-minimizer-webpack-plugin');
// const TerserPlugin = require('terser-webpack-plugin');
module.exports = {
  mode: 'production',
  devtool: 'source-map',
  /**
   * 生产环境中使用CDN，不需要下载包，排除这些库
   */
  externals: {
    // window._
    lodash: '_',
    // window.dayjs
    dayjs: 'dayjs'
  },
  // optimization: {
  //   //  生产环境中有tree shaking
  //   minimize: false,
  //   minimizer: [
  //     // 对js代码进行压缩相关的操作，开发环境不需要压缩，减少打包时间
  //     new TerserPlugin({
  //       parallel: true,
  //       extractComments: false,
  //       terserOptions: {
  //         compress: {
  //           arguments: false,
  //           dead_code: true
  //         },
  //         mangle: true,
  //         toplevel: true,
  //         keep_classnames: true,
  //         keep_fnames: true
  //       }
  //     })
  //   ]
  // },
  plugins: [
    /**
     * 该插件将 CSS 提取到单独的文件中。它为每个包含 CSS 的 JS 文件创建一个 CSS 文件。它支持 CSS 和 SourceMaps 的按需加载。它建立在新的 webpack v5 功能之上，需要 webpack 5 才能工作。
     * 异步加载
无重复编译（性能）
更容易使用
特定于 CSS
     */
    new MiniCssExtractPlugin({
      filename: 'css/[name].[contenthash:8].css',
      chunkFilename: 'css/[name].[contenthash:8].css'
    }),
    // 对css进行压缩 这样写可以只在production中使用
    new CssMinimizerPlugin()
  ]
};
/**
 * Hash、ContentHash、ChunkHash
 */
//  在我们给打包的文件进行命名的时候，会使用placeholder，placeholder中有几个属性比较相似：
// p hash、chunkhash、contenthash
// p hash本身是通过MD4的散列函数处理后，生成一个128位的hash值（32个十六进制）；
// n hash值的生成和整个项目有关系：
// p 比如我们现在有两个入口index.js和main.js；
// p 它们分别会输出到不同的bundle文件中，并且在文件名称中我们有使用hash；
// p 这个时候，如果修改了index.js文件中的内容，那么hash会发生变化；
// p 那就意味着两个文件的名称都会发生变化；
// n chunkhash可以有效的解决上面的问题，它会根据不同的入口进行借来解析来生成hash值：
// p 比如我们修改了index.js，那么main.js的chunkhash是不会发生改变的；
// n contenthash表示生成的文件hash名称，只和内容有关系：
// p 比如我们的index.js，引入了一个style.css，style.css有被抽取到一个独立的css文件中；
// p 这个css文件在命名时，如果我们使用的是chunkhash；
// p 那么当index.js文件的内容发生变化时，css文件的命名也会发生变化；
// p 这个时候我们可以使用contenthash

```

```js
//server.js
const express = require('express');
const webpack = require('webpack');
const webpackDevMiddleware = require('webpack-dev-middleware');

const app = express();
const config = require('./config/webpack.common')({}, { mode: 'development' });
const compiler = webpack(config);

// Tell express to use the webpack-dev-middleware and use the webpack.config.js
// configuration file as a base.
app.use(
  webpackDevMiddleware(compiler, {
    publicPath: config.output.publicPath
  })
);
// webpack-hot-middleware可以开启热更新
app.use(require('webpack-hot-middleware')(compiler));
// Serve the files on port 3000.
app.listen(4000, function () {
  console.log('Example app listening on port 4000!\n');
});

```

```json
//package,son
"scripts": {
    "test": "echo \"Error: no test specified\" && exit 1",
    "build": "webpack --config ./config/webpack.common.js  --mode production --env production",
    "start": "webpack serve --open  --config ./config/webpack.common.js  --mode development --env development",
    "watch": "webpack --config ./config/webpack.common.js --watch  --mode development --env development",
    "serve": "node server.js",
    "type-check": "tsc --noEmit",
    "type-check-watch": "tsc --noEmit --watch",
    "prettier": "prettier --write ."
  },
```

