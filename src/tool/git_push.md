---
Tags = ["git", "push", "branch"]
date = "2012-05-15"
title = "正确使用git避免提交冲突"
slug = "git-push"
weight = 920515
---

## 旧的个人习惯

前几年自己用过一段时间的git（原来是使用bzr，后来换成git），都是当作个人代码备份工具，没有涉及多人提交代码到中央版本库。

两个月前，我们把原来的svn版本管理换成了git，这两天提交版本时遇到许多问题，上网找些资料看，才发现用法不对，集体使用时，不能简单地再延续原来个人使用时的习惯。

背景啰嗦完了，现在进入正题：如何提交避免版本冲突。

## 团队使用的要求

首先在本地按方法1 clone 回来之后，只有一个默认分支master，不要直接在上面工作。

### 建立自己的分支

建立一个自己的分支，如取名working（最好是取个在团队中唯一的名字）： git branch working

切换到这个新分支： git checkout working

现在可以自由修改代码并保存了。

### 避免冲突

确保你修改的代码都是自己负责项目下，或者说你的两次提交之间，没有其他人来改相同项目下的代码，

如果不能避免，你就要在下面的merge步骤手工处理冲突了。

### 提交

提交代码时按下面的步骤：

可以将下面的脚本保存在你的每个项目之下，每次只修改提交一个项目。

```bash
git checkout working--force  #确保使用的是工作分支
git add .
git commit -m"$1" -a #提交代码到本地，工作分支增加一个版本，这里的$1是运行脚本的第一个参数

git checkout master
git pull origin master   #切换回默认分支，并将默认分支和中央最新版本合并
git merge working#在本地合并你的这次修改到默认分支
git push origin master   #提交到中央版本库，接下来还是要切换回工作分支的
git checkout working   --force
```

如果不小心动了生产环境（就是只从中央版本库pull到本地）的文件，只好将本地版本退回一个，再从中央代码库pull代码合并。

```bash
git reset --hard HEAD
```