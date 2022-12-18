---
title: Git Resolving Conflicts
description:
categories:
- weekly
tags:
- git
---

在 git 冲突解决时，如果能够理解双方的提交信息，解决起来自然会游刃有余。但是当冲突的内容非常多的时候，作为后备方案，正确运用 git 可以让我们少学很多东西（逃

冲突解决大概有三类方法

第一类：`git rebase` 和 `git merge`。rebase 后的提交记录更优雅，但是 merge 更容易。因为 rebase 相当于在目标（master）分支的 HEAD 节点重新把当前（ feat） 分支的每个 commit 重放一遍，这时每次 commit 都可能有冲突。但 merge 直接把目标（master）分支的 HEAD 节点和当前（feat）分支的 HEAD 节点做比较生成一个新的 commit 节点，比较只有一次。

第二类：在最新的主干 master 分支上重新拉一个 feat 分支，用 git cherry pick 或者照着 commit 重新写一遍。可以理解为手动的 `git rebase` 重放，虽然看起来费力，但是如果 master 分支经历了重构，冲突文件千疮百孔，这种方式反而要轻松一点。。

第三类：在第一类方法的基础上，再和当前的 working space 做 diff。举个例子，如果 `git merge` 有 1% 的细节没解决好，导致服务或者某个单测挂了，逐个文件去找问题非常头疼。这时候就可以用 git diff 的 CLI 或者 IDE（推荐），让当前的 working space 重新和主干分支（master）做一次 diff，再去看冲突文件的 diff 结果，因为已经解决过一次冲突（99%），所以内容已经很相似了，很容易再找出剩下 1%。

![need vpn to see this](https://raw.githubusercontent.com/thorseraq/picb/main/imgs/20221217171323.png)

综上，遇到非常难解决的冲突时，可以直接 `git merge` 加第三类方法，一把梭解决所有问题。（不过最好的办法还是尽快合入 pr，避免大冲突
