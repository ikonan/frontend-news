# 译：自由实验你的代码—Git worktree

Git worktree 是 Git 提供的一个实用功能，它能让你在同一个仓库里同时处理多个分支，而且不会影响当前的工作。比如，你可以一边修复紧急的 bug，一边开发新功能。worktree 和主工作区是相互独立的，你可以单独提交、推送和整理更改。使用 worktree 能避免频繁切换分支带来的麻烦，让工作区更整洁。这篇文章会详细介绍 worktree 的使用场景和操作方法，帮你更高效地管理 Git 仓库。

## 什么是 Git worktree

Git worktree 就像是 Git 仓库的一个链接副本，有了它，你可以同时查看多个分支。worktree 和主工作副本的存放路径不一样，而且它可以处于不同的状态，位于不同的分支上。Git 里的新 worktree 有个好处，就是你能做和当前任务无关的更改，提交这些更改，之后再把它们合并，却不会影响你现在的工作环境。

举个例子，你正在为项目开发一个新功能，这时项目经理让你进行一个紧急修复。可你的工作仓库因为正在开发新功能，比较乱。你既不想把修复工作掺和到当前的开发里，也不想先保存当前更改再创建新分支来做修复。那你就可以创建一个新的 worktree 来进行修复：

```bash
$ git branch | tee 
* dev 
  trunk 
$ git worktree add -b hotfix ~/code/hotfix trunk 
Preparing ../hotfix (identifier hotfix) HEAD is now at 62a2daf commit
```

上面这个命令的作用是，从 `trunk` 分支创建一个叫 `hotfix` 的新分支，并且把它当作一个独立的 worktree 放在 `~/code/hotfix` 目录里。之后，你就可以把这个 worktree 当成主要的工作空间。你可以切换到这个目录，进行紧急修复，提交修改，最后删除这个 worktree：

```bash
$ cd ~/code/hotfix 
$ sed -i 's/teh/the/' hello.txt 
$ git commit --all --message '紧急修复'
```

等你完成了紧急工作，就可以接着做之前的任务。你可以自己决定什么时候把这个紧急修复合并到主项目里。比如，你可以直接从它的 worktree 把更改推送到项目的远程仓库：

```bash
$ git push origin HEAD 
$ cd ~/code/myproject
```

或者你可以把 worktree 打包成 TAR 或 ZIP 文件：

```bash
$ cd ~/code/myproject 
$ git archive --format tar --output hotfix.tar master
```

又或者你可以从单独的 worktree 里获取更改：

```bash
$ git worktree list 
/home/seth/code/myproject 15fca84 [dev] 
/home/seth/code/hotfix 09e585d [master]
```

之后，你可以选择最适合你和团队的方法来合并这些更改。

## `git worktree list`

你可以用 `git worktree list` 命令来查看 worktree 的列表，还可以知道每个 worktree 对应的分支是哪一个：

```bash
$ git worktree list 
/home/seth/code/myproject 15fca84 [dev] 
/home/seth/code/hotfix 09e585d [master]
```

你在任何一个 worktree 里都能使用这个命令。worktree 之间是相互关联的（除非你手动移动它们，让 Git 找不到 worktree，这样关联就断了）。

## `git worktree move`

Git 会在项目的 `.git` 目录里记录 worktree 的位置和状态：

```bash
$ cat ~/code/myproject/.git/worktrees/hotfix/gitdir /home/seth/code/hotfix/.git
```

如果你要移动一个 worktree，就得用 `git worktree move` 命令；不然，当 Git 想更新 worktree 的状态时，就会失败：

```bash
$ mkdir ~/Temp 
$ git worktree move hotfix ~/Temp 
$ git worktree list 
/home/seth/code/myproject 15fca84 [dev] 
/home/seth/Temp/hotfix 09e585d [master]
```

## `git worktree remove`

当你完成工作后，可以用 `remove` 子命令来删除 worktree：

```bash
$ git worktree remove hotfix 
$ git worktree list 
/home/seth/code/myproject 15fca84 [dev]
```

为了保证 `.git` 目录干净，删除 worktree 后，再用 `prune` 子命令：

```bash
$ git worktree prune
```

## 何时使用 worktree

在使用标签、书签、自动备份等功能时，如果不自己记录数据，就容易造成管理混乱。Git worktree 也是如此，频繁创建新的 worktree 会导致副本过多，管理起来很麻烦。建议根据需要创建 worktree，完成任务并提交工作后，及时将其删除，以保持工作简洁和专注。

重要的是，worktree 让你管理 Git 仓库更灵活。需要的时候就用它，以后不用再为了查看另一个分支的内容而着急保存当前工作状态啦。

[英文原文](https://opensource.com/article/21/4/git-worktree)