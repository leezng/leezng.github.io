---
title: 从svn迁移到git
date: 2019-04-13 22:30:28
tags: JavaScript
---

记一次最简单的svn迁移至git的过程

<!-- more -->

- 新建一个空的git仓库: gitA
- 拉取svn代码最核心的命令

`git svn clone svn://xxx git_project`

- 单纯跑上面的命令，似乎也能完成任务，但是总觉得有点欠缺，例如committer并没有对应进行迁移，这时候便需要一个参数`--authors-file=users.txt`，`users.txt`是一份committer映射关系文件，内容大致如下：

```bash
userA = userA <userA@xxx.com>
userB = userB <userB@xxx.com>
userC = userC <userC@xxx.com>
```

- 如何生成committer映射关系（svn帐号与git帐号映射）呢？参考(网上)[https://segmentfault.com/a/1190000014713162]做法，只需执行下面指令即可：

```bash
svn log -q | awk -F '|' '/^r/ {sub("^ ", "", $2); sub(" $", "", $2); print $2" = "$2" <"$2">"}' | sort -u > users.txt
```

接着，我发现项目已经有很久的提交历史了，而此次迁移的目的仅仅只需要xx之后的版本记录，于是再添加一个 `-r 12345`   参数，此时，我们的命令已经变成`git svn clone -r 12345 svn://xxx git_project --authors-file=users.txt`

- 添加远程git仓库的跟踪

```
git remote add origin https://gitA.xxx.git
```

- 推送到远程仓库

```bash
git push "https://gitA.xxx.git" master:master
```

有些远程仓库已经有了初始化文件 `README`，这时候采取的方法是 `git push --force xxx`（因为我们是在做一个迁移动作，所以并不关心已有的文件）。通常情况下，这些命令已经能够帮助我们实现一次简单的迁移了，配合 `sourcetree` 将更加方便。
