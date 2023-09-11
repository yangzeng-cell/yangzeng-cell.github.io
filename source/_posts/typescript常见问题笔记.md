---
title: typescript常见问题笔记
date: 2023-06-17 09:23:41
tags:
- [typescript]
categories:
- [typescript, note]
---

# [解决ts开发时引入图片报错：“找不到xxx或其相应的类型声明” 的问题]

```
import img from '../../assets/images/foo.png';
```

引入文件时，提示找不到对应模块

因为typescript无法识别非代码资源。我们需要主动的去声明这个module

新建一个ts声明文件：images.d.ts

```ts
declare module '*.svg'
declare module '*.png'
declare module '*.jpg'
declare module '*.jpeg'
declare module '*.gif'
declare module '*.bmp'
declare module '*.tiff'
```

项目编译过程中会自动去读取.d.ts这种类型的文件，所以不需要我们手动加载。放置在tsconfig.json中include属性所配置的文件夹下即可。

# ['React' refers to a UMD global, but the current file is a module](https://stackoverflow.com/questions/64656055/react-refers-to-a-umd-global-but-the-current-file-is-a-module)

```
// tsconfig.json
{
  "compilerOptions": {
    ...
    "jsx": "react-jsx"
    ...
  },
}
```

or

```
import React from 'react'
```

