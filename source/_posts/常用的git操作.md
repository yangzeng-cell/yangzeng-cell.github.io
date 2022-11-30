---
title: 常用的git操作
date: 2022-09-08 16:44:11
tags:
- [Git]
categories:
- [Git, note]
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

#### 合并commit

```
git commit --amend  //将两个commit合并成一个提交
```

例如，你提交后发现忘记了暂存某些需要的修改，可以像下面这样操作：

```
git commit -m 'initial commit'
git add forgotten_file
git commit --amend
```

就是将再次commit的内容合并到之前的commit

#### 查看提交历史

```
git log

git log --pretty=oneline
git log --oneline --decorate --graph --all   //它会输出你的提交历史、各个分支的指向以及项目的分支分叉情况
```

#### 版本回退

```
git reset  --hard HEAD^

git reset  --hard HEAD~1000

git reset  --hard 2d44982
```

#### 管理远程仓库

``` <
git remote //查看远程仓库的命名

git remote -v  //查看远程仓库地址

git remote add <shortname> <url>  //添加远程仓库，让本地仓库和远程仓库建立连接

git remote rename gitlab glab  //重命名远程仓库

git remote remove gitlab  //移除远程仓库

git remote show <remote>  //获取远程仓库信息例如git remote show origin

git ls-remote <remote>  //显式地获得远程引用的完整列表

```

#### 将代码push到远程仓库：将本地仓库的代码推送到远程仓库中

```
默认情况下是将当前分支（比如master）push到origin远程仓库的；
git push

git push origin master

git push origin serverfix:awesomebranch  //远程分支和本地分支不同名时
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

有时候合并操作不会如此顺利。 如果你在两个不同的分支中，对同一个文件的同一个部分进行了不同的修改，Git 就没法干净的合并它们。 如果你对 #53 问题的修改和有关 hotfix 分支的修改都涉及到同一个文件的同一处，在合并它们的时候就会产生合并冲突：

```
$ git merge iss53
Auto-merging index.html
CONFLICT (content): Merge conflict in index.html
Automatic merge failed; fix conflicts and then commit the result.
```

此时 Git 做了合并，但是没有自动地创建一个新的合并提交。 Git 会暂停下来，等待你去解决合并产生的冲突。 你可以在合并冲突后的任意时刻使用 git status 命令来查看那些因包含合并冲突而处于未合并 （unmerged）状态的文件：

```
<<<<<<< HEAD:index.html
<div id="footer">contact : email.support@github.com</div>
=======
<div id="footer">
 please contact us at support@github.com
</div>
>>>>>>> iss53:index.html
```

这表示 HEAD 所指示的版本（也就是你的 master 分支所在的位置，因为你在运行 merge 命令的时候已经检出到了这个分支）在这个区段的上半部分（======= 的上半部分），而 iss53 分支所指示的版本在 ======= 的 下半部分。 为了解决冲突，你必须选择使用由 ======= 分割的两部分中的一个，或者你也可以自行合并这些内容。 

冲突解决后，可以使用git status 来查看是否所有的冲突都解决了，然后再使用git commit进行提交

#### Git标签（tag） - 创建tag

Git 支持两种标签：轻量标签（lightweight）与附注标签（annotated），轻量标签很像一个不会改变的分支——它只是某个特定提交的引用，而附注标签是存储在 Git 数据库中的一个完整对象， 它们是可以被校验的，其中包含打标签者的名字、电子邮件 地址、日期时间， 此外还有一个标签信息，并且可以使用 GNU Privacy Guard （GPG）签名并验证。 通常会建议创建附注标签，这样你可以拥有以上所有信息。但是如果你只是想用一个临时的标签， 或者因为某些原因不想要保存这些信息，那么也可以用轻量标签

```
git tag v1.0  //轻量标签

git tag -a v1.1 -m "附注标签"

git show v1.4  //可以看到标签信息和与之对应的提交信息 输出显示了打标签者的信息、打标签的日期时间、附注信息，然后显示具体的提交信息
```

#### **列出标签**

```
git tag （可带上可选的 -l 选项 --list）

git tag -l "v1.8.5*"  //列出v1.8.5开头的标签
```

#### push tag

```
git push origin v1.0

git push origin --tags  //推送所有标签
使用 git push <remote> --tags 推送标签并不会区分轻量标签和附注标签， 没有简单的
选项能够让你只选择推送一种标签。
```

#### 删除本地tag

```
git tag -d v1.1
```

#### 删除远程tag

```
git push origin  --delete v1.1  //第一种方式

 git push <remote> :refs/tags/<tagname>  //第二种方式
```

#### 切换到目标tag版本

```
git checkout v1.0   //虽然这会使你的仓库处于“分离头指针（detached HEAD）”的状态——这个状态有些不好的副作用
```

在“分离头指针”状态下，如果你做了某些更改然后提交它们，标签不会发生变化， 但你的新提交将不属于任何分支，并且将无法访问，除非通过确切的提交哈希才能访问。 因此，如果你需要进行更改，比如你要修复旧版本中的错误，那么通常需要创建一个新分支：

```
$ git checkout -b version2 v2.0.0
```

如果在这之后又进行了一次提交，version2 分支就会因为这个改动向前移动， 此时它就会和 v2.0.0 标签稍 微有些不同，这时就要当心了。

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
git branch  查看当前所有分支  带*就是head指针指向的分支，一般是当前分支
git brabch -v  查看最后一次提交

git branch --merged 查看所有合并到当前分支的分支

git branch --no-merged 查看所有没有合并到当前分支的分支  没有合并的分支不允许删除如果需要强制性删除 git branch -D hotfix

git branch --no-merged master  还没有合并到master的分支

git branch -d hotfix  删除hotfix分支
```

#### 远程分支的管理

```
git push <remote> <branch>  推送远程分支

git checkout -b serverfix origin/serverfix  //创建分支并跟踪远程分支 下面是他的简写

git checkout --track <remote>/<branch> 跟踪远程分支

git checkout -b sf origin/serverfix //创建和远程分支不一样的名字

git push origin --delete <branch> 删除远程分支
```

#### Git rebase用法

```
git checkout experiment
git rebase master
git checkout master
git merge experiment
```

它的原理是首先找到这两个分支（即当前分支 experiment、变基操作的目标基底分支 master） 的最近共同祖先 C2，然后对比当前分支相对于该祖先的历次提交，提取相应的修改并存为临时文件， 然后将当前分支指向目标基底 C3, 最后以此将之前另存为临时文件的修改依序应用

在对两个分支进行变基时，所生成的“重放”并不一定要在目标分支上应用，你也可以指定另外的一个分支进行 应用。 就像 从一个主题分支里再分出一个主题分支的提交历史 中的例子那样。 你创建了一个主题分支server，为服务端添加了一些功能，提交了 C3 和 C4。 然后从 C3 上创建了主题分支 client，为客户端添加了一些功能，提交了 C8 和 C9。 最后，你回到 server 分支，又提交了 C10。

假设你希望将 client 中的修改合并到主分支并发布，但暂时并不想合并 server 中的修改， 因为它们还需要经过更全面的测试。这时，你就可以使用 git rebase 命令的 --onto 选项， 选中在 client 分支里但不在server 分支里的修改（即 C8 和 C9），将它们在 master 分支上重放：

```
git rebase --onto master server client
```

以上命令的意思是：“取出 client 分支，找出它从 server 分支分歧之后的补丁， 然后把这些补丁在master 分支上重放一遍，让 client 看起来像直接基于 master 修改一样”

接下来你决定将 server 分支中的修改也整合进来。 使用 git rebase <basebranch> <topicbranch> 命令可以直接将主题分支 （即本例中的 server）变基到目标分支（即 master）上。 这样做能省去你先切换到server 分支，再对其执行变基命令的多个步骤

```
git rebase master server 

相当于
git checkout server
git rebase master
```

##### 变基的风险

如果提交存在于你的仓库之外，而别人可能基于这些提交进行开发，那么不要执行变基

##### 用变基解决变基

 如果团队中的某人强制推送并覆盖了一些你所基于的提交，你需要做的就是检查你做了哪些修改，以及他们覆盖了哪些修改。实际上，Git 除了对整个提交计算 SHA-1 校验和以外，也对本次提交所引入的修改计算了校验和——即 “patchid”。 

如果你拉取被覆盖过的更新并将你手头的工作基于此进行变基的话，一般情况下 Git 都能成功分辨出哪些是你的 

修改，并把它们应用到新分支上。

举个例子，如果遇到前面提到的 有人推送了经过变基的提交，并丢弃了你的本地开发所基于的一些提交 那种情 

境，如果我们不是执行合并，而是执行 git rebase teamone/master, Git 将会： 

• 检查哪些提交是我们的分支上独有的（C2，C3，C4，C6，C7） 

• 检查其中哪些提交不是合并操作的结果（C2，C3，C4） 

• 检查哪些提交在对方覆盖更新时并没有被纳入目标分支（只有 C2 和 C3，因为 C4 其实就是 C4'） 

• 把查到的这些提交应用在 teamone/master 上面

从而我们将得到与 你将相同的内容又合并了一次，生成了一个新的提交 中不同的结果。在一个被变基然后强制推送的分支上再次执行变基。

在本例中另一种简单的方法是使用 `git pull --rebase` 命令而不是直接 git pull。 又或者你可以自己手动 完成这个过程，先 git fetch，再 git rebase teamone/master。 

如果你习惯使用 git pull ，同时又希望默认使用选项 --rebase，你可以执行这条语句 git config --global pull.rebase true 来更改 pull.rebase 的默认配置。

如果你只对不会离开你电脑的提交执行变基，那就不会有事。 如果你对已经推送过的提交执行变基，但别人没 有基于它的提交，那么也不会有事。 如果你对已经推送至共用仓库的提交上执行变基命令，并因此丢失了一些别人的开发所基于的提交， 那你就有大麻烦了，你的同事也会因此鄙视你。 

如果你或你的同事在某些情形下决意要这么做，请一定要通知每个人执行 git pull --rebase 命令，这样尽管不能避免伤痛，但能有所缓解。

#### 撤销修改

```
对于未进行提交到暂存区域的
git checkout -- <file>
```

请务必记得 git checkout -- <file> 是一个危险的命令。 你对那个文件在本地的任何修改都会消失——Git 会用最近提交的版本覆盖掉它。 除非你确实清楚不想要对那个文件的本地修改了，否则请不要使用这个命令。

#### **Git** 别名

为git 命令设置别名

```
git config --global alias.co checkout
git config --global alias.br branch
git config --global alias.ci commit
git config --global alias.st status
```

这意味着，当要输入 git commit 时，只需要输入 git ci。

```
git config --global alias.unstage 'reset HEAD --'
```

这会使下面的两个命令等价：

```
git unstage fileA

git reset HEAD -- fileA
```

这样看起来更清楚一些。 通常也会添加一个 last 命令，像这样：

```
git config --global alias.last 'log -1 HEAD'
```

使用自定义指令来查看最后一次提交信息

```
git last
```

可以看出，Git 只是简单地将别名替换为对应的命令。 然而，你可能想要执行外部命令，而不是一个 Git 子命令。 如果是那样的话，可以在命令前面加入 ! 符号。 如果你自己要写一些与 Git 仓库协作的工具的话，那会很有用。 我们现在演示将 git visual 定义为 gitk 的别名：

```
git config --global alias.visual '!gitk'
```

#### 引用**日志**

当你在工作时， Git 会在后台保存一个引用日志（reflog）， 引用日志记录了最近几个月你的 HEAD 和分支引用 所指向的历史。 

你可以使用 git reflog 来查看引用日志，引用日志只存在于本地仓库

```
git reflog
```

```
$ git reflog
734713b HEAD@{0}: commit: fixed refs handling, added gc auto, updated
d921970 HEAD@{1}: merge phedders/rdocs: Merge made by the 'recursive'
strategy.
1c002dd HEAD@{2}: commit: added some blame and merge stuff
1c36188 HEAD@{3}: rebase -i (squash): updating HEAD
95df984 HEAD@{4}: commit: # This is a combination of two commits.
1c36188 HEAD@{5}: rebase -i (squash): updating HEAD
7e05da5 HEAD@{6}: rebase -i (pick): updating HEAD
```

```
git show HEAD@{5}    //如果你想查看仓库中 HEAD 在五次前的所指向的提交，你可以使用 @{n} 来 引用 reflog 中输出的提交记录。

git show master@{yesterday}   //查看你的 master 分支在昨天的时候指向了哪个提交，这个方法只对还在你引用日志里的数据有用，所以不能用来查好几个月之前的提交。
```

#### 祖先**引用**

```
git show HEAD^   // 可以查看上一次的提交  在window中不可用
git show HEAD^^  // win中可以使用
git show "HEAD^" // win中要使用“”
git show d921970^ //某次提交的上一次提交 
```

另一种指明祖先提交的方法是 ~（波浪号）

```
git show HEAD~3  也可以使用  git show HEAD～～～
```

#### 提交区**间**

可以用来制定那些提交需要合并到master分支

##### 双点

这种语法可以选出在一个分支中而不再另外一个分支的中的提交

例如：

​	你想要查看 experiment 分支中还有哪些提交尚未被合并入 master 分支

```
git log master..experiment
```

​	如果你想查看在 master 分支中而不在 experiment 分支中的提交，你只要交换分支名即可

```
git log experiment..master
```

​	另一个常用的场景是查看你即将推送到远端的内容‘

```
git log origin/master..HEAD
```

#### 暂存

```
git stash/git stash push

git stash list //获取暂存列表

git stash apply //应用最新的暂存文件

git stash apply stash@{2}  //应用指定的暂存文件

git stash pop //应用暂存并且删除

git stash drop //删除暂存
```

#### 重**写历史**

##### 修改最后一次提交

```
 git commit --amend
```

##### 合并多次提交信息

```
git rebase -i HEAD~3
```



## 技巧

```
git branch --set-upstream-to=origin/master  //设置上游分支就可以只写 git pull了

git merge --allow-unrelated-histories  //强制合并

git push origin head:main  head默认指向main
如果要push master默认要这么写git push origin master:main  如果想写git push就要这么写
配置git push 指向上游路径master，就是配置好的git branch --set-upstream-to=origin/master
调用git push 就会push到master,但是这样每个分支都是push到master
git config push.default upstream  [--global]//加上global可以全局使用，否则就是当前项目使用

git config push.default current  //这个会先去仓库找有没有和本地相同的分支，如果没有就在远程创建一个新的和本地相同的分支

push.default 默认是simple 就是push相同的分支
```

##### [Fast-forward merge is not possible. To merge this request, first rebase locally](https://stackoverflow.com/questions/53066369/fast-forward-merge-is-not-possible-to-merge-this-request-first-rebase-locally)

解决办法

`git checkout master` 先切换到主分支

`git pull origin master` 然后再pull分支下来

`git checkout newBranch` 切换到当前分支

`git rebase origin/master -i`    再rebase

`git rebase --continue`
