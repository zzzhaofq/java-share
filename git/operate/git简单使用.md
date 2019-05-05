# git简单使用
  项目上使用git较多，以后也会经常使用git，对git的一些常用命令，主要涉及到比较浅git功能：克隆项目-创建分支-提交内容。以github作为远程仓库为例，并不会介绍如何连接github上。

*1. clone项目*

在已配好的github上创建一个仓库，并clone项目到本地来

  >git clone https://github.com/**********/note.git

*2.拉取下来默认是master分支，master分支是最终发布版的内容，所以我们创建新分支test。*
  > git checkout -b test
  >
  > git push origin test:test


git checkout因为没有指定远程分支（还没有对应的远程分支），是在本地仓库上创建一分支。</br>
git push 远程仓库名 远程分支:本地分支；提交远程分支未找到其分支系统将自动创建。使用git branch -a查看所有分支，会看到remotes/origin/test这个远程分支，说明新建远程分支成功

*3. 将内容提交至本地git仓库*
  >git add -A
  >
  >git status
  >
  >git commit -m "first commit"

git add -A 将所有的变更的文件（新增、修改、删除）的文件都放到暂存区中。
git status 确认下是否所有的变更的文件都方到暂存区中。
git commit 是将暂存区的内容提交到本地git仓库中。

*4.本地git仓库推送到远程仓库中*
  >git push origin test:test

远程git仓库已收到由本地push上的内容。这时在test分支上可以查看到刚提交的内容。那么我们的目的是在test上操作，最后需要合并到master这条主干上。

*5.切换master分支并保证master内容是最新*
  >git checkout master
  >
  >git pull
  >
  >git status

git checkout master首先切换到master分支;</br>
git pull是拉取master分支上的内容（可能并行的版本已经提交到master上，保证master上是最新内容）;</br>
git status查看自己是否又在本地

*6.master合并test内容*
  >git merge origin/test

在master分支下执行上述命令，将test的内容合并到master上。
