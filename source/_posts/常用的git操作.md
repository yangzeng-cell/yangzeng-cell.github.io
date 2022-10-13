---
title: 常用的git操作
date: 2022-09-08 16:44:11
tags:
categories:
- [git, note]
---



#### ##git基本使用

##### 初始化Git仓库

```
git init
```

##### 克隆仓库

```
git clone https://github.com/coderwhy/hy-react-web-music.git
```

#### 检测文件的状态

```
git status

git status -s

git status --short
```

#### 将所有文件添加到暂存区

```
git add .
```

#### 将暂存区的文件提交

```
git commit -m "提交信息"
```

#### add commit 进行合并操作

```
git commit -a -m "修改文件"
```

#### 查看提交历史

```
git log

git log --pretty=oneline
```

#### 版本回退

```
git reset  --hard HEAD^

git reset  --hard HEAD~1000

git reset  --hard 2d44982
```

#### 管理远程仓库

``` <
git remote -v  //查看远程仓库地址

git remote add <shortname> <url>  //添加远程仓库，让本地仓库和远程仓库建立连接

git remote rename gitlab glab  //重命名远程仓库

git remote remove gitlab  //移除远程仓库
```

#### 将代码push到远程仓库：将本地仓库的代码推送到远程仓库中

```
默认情况下是将当前分支（比如master）push到origin远程仓库的；
git push

git push origin master
```

#### 从远程仓库fetch代码：从远程仓库获取最新的代码

```
默认情况下是从origin中获取代码；

git fetch

git fetch origin

获取到代码后默认并没有合并到本地仓库，我们需要通过merge来合并；

git merge
```

####  从远程仓库pull代码：上面的两次操作有点繁琐，我们可以通过一个命令来操作

```
git pull ===git fetch + git merge(rebase)
```

#### 合并冲突



#### Git标签（tag） - 创建tag

```
git tag v1.0

git tag -a v1.1 -m "附注标签"
```

#### push tag

```
git push origin v1.0

git push origin --tags
```

#### 删除本地tag

```
git tag -d v1.1
```

#### 删除远程tag

```
git push origin  --delete v1.1
```

#### 切换到目标tag版本

```
git checkout v1.0
```

#### Git创建分支

```
git branch testing
```

#### Git切换分支

```
git checkout testing
```

#### 创建分支同时切换

```
git checkout -b testing
```

#### 分支开发与合并

```
git checkout master
git merge hotfix
```

#### 查看和删除分支

```
git branch  查看当前所有分支
git brabch -v  查看最后一次提交

git branch --merged 查看所有合并到当前分支的分支

git branch --no-merged 查看所有没有合并到当前分支的分支
```

#### 远程分支的管理

```
git push <remote> <branch>  推送远程分支

git checkout --track <remote>/<branch> 跟踪远程分支

git push origin --delete <branch> 删除远程分支
```

#### Git rebase用法

```
git checkout experiment
git rebase master
git checkout master
git merge experiment
```

