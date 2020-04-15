---
url: /2020/03/26/git-command.html
title: "Git 常用命令"
keywords: "Git 常用命令"
description: "Git 常用命令"
date: 2020-03-26T22:53:12+08:00
draft: false
tags: ["Git"]
tags_weight: 100
categories: ["Git"]
categoryes_weight: 100
---
## git init
初始化本地git仓库（创建新仓库）

`git init` 创建普通库

`git init --bear` 创建裸库
```
当你创建一个普通库时，在工作目录下，除了.git目录之外，你还可以看到库中所包含的所有源文件。
你拥有了一个可以进行浏览和修改（add, commit, delete等）的本地库

当你创建一个裸库时，在工作目录下，只有一个.git目录，而没有类似于本地库那样的文件结构可供你直接进行浏览和修改。
一般来说，一个裸库往往被创建用于作为大家一起工作的共享库，每一个人都可以往里面push自己的本地修改。
项目团队里面的每个人都可以clone这个库，然后完成本地修改之后，往这个库中push自己的代码。
```

## git config
配置git的一些参数

`git config user.name "xxx"` 配置用户名

`git config user.email "xxx@xxx.com"` 配置邮箱

`git config --global color.ui true` git status等命令自动着色

git对CRLF的处理配置

`git config --global core.autocrlf true` 提交时转换为LF，检出时转换为CRLF

`git config --global core.autocrlf input` 提交时转换为LF，检出时不转换

`git config --global core.autocrlf false` 提交检出均不转换

`git config --global core.safecrlf true` 拒绝提交包含混合换行符的文件

`git config --global core.safecrlf false` 允许提交包含混合换行符的文件

`git config --global core.safecrlf warn` 提交包含混合换行符的文件时给出警告

加上 `--global` 表示全局配置，不加表示仅该仓库配置

## git clone

clone远程仓库

`git clone git+ssh://git@192.168.53.168/VT.git`

## git submodule

`git submodule add https://github.com/wenjy/maupassant-hugo.git themes/maupassant`

`git submodule update --remote` 更新子模块为远程项目的最新版本

```
git Submodule 是一个很好的多项目使用共同类库的工具，他允许类库项目做为repository，子项目做为一个单独的git项目存在父项目中，
子项目可以有自己的独立的commit，push，pull。而父项目以Submodule的形式包含子项目，父项目可以指定子项目header，
父项目中会的提交信息包含Submodule的信息，再clone父项目的时候可以把Submodule初始化。
```

## git status
                                                
查看当前版本状态（是否修改）

## git add

添加文件至index（暂存区）

`git add xyz ` 添加xyz文件至index

`git add .` 增加当前子目录下所有更改过的文件至index

## git commit

把暂存区的文件提交到本地仓库

`git commit -m 'xxx'` 提交并备注信息

`git commit --amend -m 'xxx'` 合并上一次提交（用于反复修改）

`git commit -am 'xxx'` 将add和commit合为一步

## git rm

删除index中的文件

`git rm xxx` 删除index中的xxx文件

`git rm -r *` 递归删除

## git log

显示提交日志

`git log` 显示全部的

`git log -1` 显示1行日志 -n为n行

`git log --stat` 显示提交日志及相关变动文件

`git log -p -m` 更加详细的文件改动 diff

`git log v2.0` 显示tag v2.0的日志

`git log --pretty=format:'%h' --graph` 图示提交日志

## git show

显示某个提交的详细内容

`git show dfb02e6e4f2f7b573337763e5c0013802e392818` 显示某个提交的详细内容

`git show dfb02` 可只用commitid的前几位，一般不会重复

`git show HEAD` 显示HEAD提交日志

`git show HEAD^` 显示HEAD的父（上一个版本）的提交日志 ^^为上两个版本 ^5为上5个版本

`git show v2.0` 显示tag v2.0的日志及详细内容

`git show master@{yesterday}` 显示master分支昨天的状态


## git ls-files                                              
列出git index包含的文件

## git show-branch                                           
显示当前分支历史

`git show-branch --all` 显示所有分支历史

## git whatchanged                                          
显示提交历史对应的文件修改

## git reflog                                                
显示所有提交，包括孤立节点

```
什么叫孤立节点，git commit id 组成的树是单向链表，当前commit只指向父级的commit，当HEAD指向头指针
我们每次commit merge 等操作都有可能出现不能追踪的commit。
```

## git tag

`git tag` 显示已存在的tag

`git tag -a v2.0 -m 'xxx'` 增加v2.0的tag


## git diff
显示所有未添加至index的变更

`git diff` 显示所有未添加至index的变更

`git diff --cached` 显示所有已添加index但还未commit的变更

`git diff HEAD^` 比较与上一个版本的差异

`git diff HEAD -- ./lib` 比较与HEAD版本lib目录的差异

`git diff origin/master..master` 比较远程分支master上有本地分支master上没有的

`git diff origin/master..master --stat` 只显示差异的文件，不显示具体内容

## git remote
关于远程分支操作


`git remote add origin git+ssh://git@192.168.53.168/VT.git` 增加远程定义（用于push/pull/fetch）

`git remote rename origin origin_new` 重命名

`git remote remove origin_new` 删除远程追踪

`git remote show` 查看远程追踪

`git remote show origin` 查看远程追踪的详细信息

## git branch
显示本地分支


`git branch` 显示本地分支

`git branch --contains 50089` 显示包含提交50089的分支

`git branch -a` 显示所有分支

`git branch -r` 显示所有原创分支

`git branch --merged` 显示所有已合并到当前分支的分支

`git branch --no-merged` 显示所有已合并到当前分支的分支

`git branch -m master master_copy` 本地分支改名

`git branch -d hotfixes/BJVEP933` 删除分支hotfixes/BJVEP933（本分支修改已合并到其他分支）

`git branch -D hotfixes/BJVEP933` 强制删除分支hotfixes/BJVEP933

## git checkout
检出分支


`git checkout -b master_copy` 从当前分支创建新分支master_copy并检出

`git checkout -b master master_copy` 上面的完整版

`git checkout features/performance` 检出已存在的features/performance分支

`git checkout --track hotfixes/BJVEP933` 检出远程分支hotfixes/BJVEP933并创建本地跟踪分支

`git checkout v2.0` 检出版本v2.0

`git checkout -b develop origin/develop` 从远程分支develop创建新本地分支develop并检出

`git checkout -- README` 检出head版本的README文件（可用于修改错误回退），可以简略`--`，加上是为了避免和分支 tag冲突

## git merge
分支合并


`git merge origin/master` 合并远程master分支至当前分支

```
fast-forward方式就是当条件允许的时候，git直接把HEAD指针指向合并分支的头，完成合并。
属于“快进方式”，不过这种情况如果删除分支，则会丢失分支信息。因为在这个过程中没有创建commit
```

`git merge --no-ff origin/master` 强行关闭fast-forward方式，不使用fast-forward方式合并，保留分支的commit历史

`git merge --squash origin/master` 把一些不必要commit进行压缩，把多次分支commit历史压缩为一次


## git cherry-pick
合并提交

`git cherry-pick ff44785404a8e` 合并提交ff44785404a8e的修改
```
可以选择某一个分支中的一个或几个commit(s)来进行操作。
例如，假设我们有个稳定版本的分支，叫v2.0，另外还有个开发版本的分支v3.0，我们不能直接把两个分支合并，这样会导致稳定版本混乱，
但是又想增加一个v3.0中的功能到v2.0中，这里就可以使用cherry-pick了,其实也就是对已经存在的commit 进行再次提交。
```
`git cherry-pick (--continue | --quit | --abort)` 解决冲突使用

`git cherry-pick -x <commit id>` 保留原提交者信息

`git cherry-pick <start-commit-id>..<end-commit-id>` Git从1.7.2版本开始支持批量cherry-pick，就是一次可以cherry-pick一个区间的commit

`git cherry-pick <start-commit-id>^..<end-commit-id>`
```
前者表示把<start-commit-id>到<end-commit-id>之间(左开右闭，不包含start-commit-id)的提交cherry-pick到当前分支；
后者有"^"标志的表示把<start-commit-id>到<end-commit-id>之间(闭区间，包含start-commit-id)的提交cherry-pick到当前分支。
其中，<start-commit-id>到<end-commit-id>只需要commit-id的前6位即可，并且<start-commit-id>在时间上必须早于<end-commit-id>
```


## git push
提交分支修改到远程分支

`git push origin master` 将当前分支push到远程master分支

`git push origin :hotfixes/BJVEP933` 删除远程仓库的hotfixes/BJVEP933分支

`git push --tags` 把所有tag推送到远程仓库

## git fetch
获取所有远程分支（不更新本地分支，另需merge）到FETCH_HEAD

`git fetch --prune` 获取所有原创分支并清除服务器上已删掉的分支

## git pull
获取所有远程分支并合并

`git pull origin master` 获取远程分支master并merge到当前分支

与 `git fetch`相比，差了一个merge操作


## git mv
重命名文件

`git mv README README2` 重命名文件README为README2

## git reset
回滚操作

`git reset --hard HEAD` 将当前版本重置为HEAD（通常用于merge失败回退），会导致工作区内容“丢失”

`git reset HEAD <file>...` 取消暂存的文件

```
在使用 hard 选项时，一定要确保知道自己在做什么，不要在迷糊的时候使用这条选项。
如果真的误操作了，也不要慌，因为只要 git 一般不会主动删除本地仓库中的内容，根据你丢失的情况，可以进行找回，
比如在丢失后可以使用 git reset --hard ORIG_HEAD 立即恢复，或者使用 git reflog 命令查看之前分支的引用。
```

`git reset --mixed HEAD` mixed 比 soft 的作用域多了一个 暂存区。实际上 mixed 选项与 soft 只差了一个 add 操作。

`git reset --soft HEAD` 会仅仅修改分支指向。而不修改工作区与暂存区的内容，我们可以接着做一次提交，形成一个新的 commit。这在我们撤销临时提交的场景下显得比较有用


## git rebase
```
rebase 会把从 Merge Base 以来的所有提交，以补丁的形式一个一个重新达到目标分支上。
这使得目标分支合并该分支的时候会直接 Fast Forward，即不会产生任何冲突。提交历史是一条线。
rebase 的一个缺点，那就是修改了分支的历史提交。如果已经将分支推送到了远程仓库，会导致无法将修改后的分支推送上去，必须使用 -f 参数（force）强行推送（怕不怕被打？）。
所以使用 rebase 最好不要在公共分支上进行操作。
```

## git revert
撤销提交


`git revert dfb02e6` 撤销提交dfb02e6
```
如果我们想要用一个反向提交恢复项目的某个版本，那就需要 revert 来协助我们完成了。
什么是反向提交呢，就是旧版本添加了的内容，要在新版本中删除，旧版本中删除了的内容，要在新版本中添加。
这在分支已经推送到远程仓库的情境下非常有用。
revert 也不会修改历史提交记录，实际的操作相当于是检出目标提交的项目快照到工作区与暂存区，然后用一个新的提交完成版本的“回退”。
```

## git stash
暂存当前修改，将所有至为HEAD状态

`git stash list` 查看所有暂存

`git stash show -p stash@{0}` 参考第一次暂存

`git stash apply stash@{0}` 应用第一次暂存

```
有时，我们在一个分支上做了一些工作，修改了很多代码，而这时需要切换到另一个分支干点别的事。但又不想将只做了一半的工作提交。
在曾经这样做过，将当前的修改做一次提交，message 填写 half of work，然后切换另一个分支去做工作，完成工作后，切换回来使用 reset —soft 或者是 commit amend。

git 为了帮我们解决这种需求，提供了 stash 命令。

stash 将工作区与暂存区中的内容做一个提交，保存起来，然后使用reset hard选项恢复工作区与暂存区内容。我们可以随时使用 stash apply 将修改应用回来。

stash 实现思路将我们的修改提交到本地仓库，使用特殊的分支指针（.git/refs/stash）引用该提交，然后在恢复的时候，将该提交恢复即可。
```

## git grep
搜索文本

`git grep "delete from"` 文件中搜索文本“delete from”

参考链接：

[git 命令](https://gist.github.com/guweigang/9848271)

[git 原理](https://blog.coding.net/blog/principle-of-git)

[git官方文档](https://git-scm.com/docs)
