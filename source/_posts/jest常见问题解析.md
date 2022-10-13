---
title: jest常见问题解析
date: 2022-09-19 20:55:59
tags:
categories:
- [单元测试,jest]
---

#### ##jest 'beforeEach' is not defined .eslint no-undef

![QQ截图20220919211238.png](https://img1.imgtp.com/2022/09/19/mkE2sSaP.png)

需要在.eslintrc文件加上这一段

```
{
  "overrides": [
    {
      "files": [
        "**/*.spec.js",
        "**/*.spec.jsx"
      ],
      "env": {
        "jest": true
      }
    }
  ]
}
```

