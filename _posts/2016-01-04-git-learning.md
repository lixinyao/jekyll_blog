---
layout: post
title: 拥抱git的世界
categories: [git］
tags:
- git
- github
---

又是很久没有更新博客了，最近突发神经想要学习一下git，在此总结一些学习的东西

git是具有纪录**历次代码改动**和**协同合作**两个功能的**分布式版本控制系统**，主要分为工作区、暂存区、当前分支、远程分支四部分

# 安装git

```
sudo apt-get install git
```

# 配置git

```
git config --global user.name "lixinyao"
git config --global user.email "slylixinyao@gmail.com"
```

# git初始化

将一个目录变为git可管理的目录

```
git init
```

# 将文件提交并提交到repository

`git add`将文件添加到暂存区，`git commit`将暂存区的内容提交到当前分支

```
git add <文件名>
git commit -m "提交说明"
```

# 查看repository的状态

```
git status
```

# 查看文件的区别，修改的内容

```
git diff
```

# 查看历次版本修改

```
git log --pretty=oneline
```

# HEAD

在git里，`HEAD`指向当前版本，`HEAD^`指向上个版本，`HEAD~100`指向前100的版本

# 版本回退

回退到上个版本

```
git reset --hard HEAD^
```

# 查看自己的历史命令，找commit id

```
git reflog
```

# 撤销工作区的修改(未add到暂存区)

用版本库的文件覆盖到工作区

```
git checkout -- filename
```

# 将暂存区的内容撤销回工作区，再撤销工作区内容

```
git reset HEAD filename
git checkout -- filename
```

如不但内容修改错误，还提交到了repository，版本回退即可

# 版本库删除文件

版本库删除文件后，还需提交

```
git rm filename
git commit -m ""
```

# 创建SSH key

```
ssh -keygen -t rsa -C "slylixinyao.gmail.com"
```

id_rsa是私钥，id_rsa.pub是公钥。登陆Github，打开account settings的SSH key页面，黏贴id_rsa.pub的内容，就可以有权限将电脑的文件推送到远程仓库了

# 创建远程仓库

在Github里先创建一个repo，比如lixinyao.github.io，再将本地的仓库添加到远程。在本地目录下执行

```
git remote add origin git@git.com:lixinyao/lixinyao.github.io.git
```

# 将本地内容推送到远程

第一次推送要加-u，以后就不用了

```
git push -u origin master
```

# 从远程库克隆

```
git clone git@github.com:lixinyao/lixinyao.github.io.git
```

# 创建dev分支并切换到dev

```
git branch dev
git checkout dev
```

`git branch`查看所有分支，当前分支在前面标注`*`

# 合并分支到当前分支

```
git merge dev
```

# 删除分支

```
git branch -d dev
```

# 有冲突无法快速合并的情况先解决冲突

查看分支的合并情况

```
git log --graph --pretty=oneline -abbrev-commit
```

# 分支管理

使用fast forward模式合并分支，在删除分支后就会丢掉分支信息，可以禁用fast forward

```
git merge --no-ff -m "merge with no-ff" dev
```

合并后用git log查看分支历史

```
git log --graph --pretty=oneline --abbrev-commit
```

# 储藏现场工作

假如工作进行到一半，不能提交，但是需先解决bug，可先储藏现在的工作现场

```
git stash
```

查看储藏的工作现场

```
git stash list
```

恢复工作现场，stash内容不删除，需用gis stash drop删除

```
git stash apply
git stash drop
```

恢复的同时删除stash

```
git stash pop
```

恢复指定的stash

```
git stash apply stash@{0}
```

# 强行删除分支

```
git branch -D test
```

# 查看远程仓库信息

```
git remote -v
```

# 推送到dev分支

```
git push origin dev
```

# 将最新的提交从远程上抓去下来

```
git pull
```

# 在本地创建和远程分支对应的分支

```
git checkout -b branch-name origin/branch-name
```

# 建立本地分支和远程分支的链接

```
git branch --set-upstream branch-name origin/branch-name
```

# 标签管理

创建标签和查看标签的命令

```
git tag <name>
git tag
git tag <name> commit id
git show <tagname>
```

删除标签

```
git tag -d v0.1
```

推送标签到远程

```
git push origin v1.0
```

一次推送所有未推送的标签

```
git push origin --tags
```

删除远程标签

```
git push origin :refs/tags/<tagname>
```
