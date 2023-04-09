---
title: Mac 安装Nginx详细教程
date: 2023-04-09 15:01:53
tags:
- [Nginx]
categories:
- [Nginx]
---

1.首先在mac中安装nginx首先要安装homebrew

2.使用brew安装nginx，如下命令所示：

```
brew install nginx
```

3.查看nginx的配置信息，如下命令：

```
brew info nginx
```

4.在终端输入 ps -ef|grep nginx 命令看是否有启动

```
ps -ef|grep nginx
```

5.启动nginx

```
brew services start nginx
```

6.重启nginx

```
brew services restart nginx
```

