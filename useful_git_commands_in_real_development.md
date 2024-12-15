# 实际开发中使用 Git 来管理项目的流程

本笔记中所使用的 Git 的版本为 : `git version 2.46.0.windows.1`

## Git Introduction

### 0.0 Git 工作流程

![git_workflow.webp](D:\hack_workbench\note\note_pic\git_workflow.webp)

在 Git 工作流程中，有几个关键词是理解版本控制的关键，它们分别是：Remote、Repository、Index（也称为 Staging Area）和 WorkSpace，下面我将逐一解释这些概念 : 

* Repository（仓库）: 
  * 定义 : Repository 是 Git 中存储项目所有文件、历史版本、分支、标签等信息的中心地点，一个 Repository 可以是本地的（存储在你的计算机上），也可以是远程的（存储在服务器上）
  * 作用 : Repository 是 Git 进行版本控制的核心，它跟踪文件的变化，记录每次更改和提交，使得你能够恢复到之前的状态，或者查看项目的演变历史
* Remote（远程仓库）:
  * 定义 : Remote 是指存储在远程服务器上的 Repository，通常，团队成员会将他们的本地 Repository 与一个或多个远程 Repository 关联，以便团队成员之间可以共享代码、提交更改和协同工作
  * 作用 : Remote 仓库是团队成员进行代码提交、拉取和推送更新的地方，使得多人可以同时在同一个项目上工作，而不会产生冲突
* Index（索引）或 Staging Area（暂存区）:
  * 定义 : Index，也称为 Staging Area，是 Git 中一个特殊的区域，用于暂存你准备在下次提交（Commit）中包含的文件和更改
  * 作用 : 当你修改了项目中的文件，Git 不会自动将这些更改包含在下一次提交中，相反，你必须手动将这些更改添加到 Index 中，这个过程称为 "Staging"，这样，你可以在提交前决定哪些更改应该被记录
* WorkSpace（工作区）:
  * 定义 : WorkSpace 是指你正在其中工作和编辑文件的本地目录，这是你编辑代码、修改文件的地方，所有未提交（Add）的更改都保存在这里
  * 作用 : WorkSpace 是你的本地开发环境，你可以在其中进行所有代码编辑和测试，当你准备好保存更改时，你将把它们从 WorkSpace 移动到 Index，然后提交到 Repository

在 Git 的工作流程中，你通常会在 WorkSpace 中编辑文件，然后将更改暂存到 Index，最后提交更改到 Repository，如果有远程 Repository，你还可以将本地的提交推送到远程仓库，以保持与团队的同步 .

但值得注意的是，我感觉上面这个图有点问题，`pull` 这个箭头应该由 `Remote` 指向 `Repository`，而不是指向 `WorkSpace` .

### 0.1 Git 分支的概念

![git_branch.png](D:\hack_workbench\note\note_pic\git_branch.png)



## Clone

### 1.1 从远程仓库获取源代码

```bash
$ git clone [url]
```



## Init

### 1.2 在本地新建仓库

当然，如果不是从远程仓库获取 git 项目，那就是另一种常见情况啦，即将本地指定目录设置成 Git WorkSpace : 

```bash
$ git init
```

然后，配置下 git 的用户信息，这些信息在后面查看代码提交记录、及将代码 push 到远程仓库时非常有用，对了，如果你只想让此 git 用户信息的作用域限制在该项目，在不要使用参数 --global .

```bash
$ git config --global user.name 'your name'
$ git config --global user.email 'your_name@example.com'
```

配置完成后可以通过下述命令来查看刚刚配置的信息 : 

```bash
$ git config user.name
$ git config user.email
```

当然，你也可以查看当前 git 的全部配置信息 : 

```bash
$ git config --list
```

>- "/etc/gitconfig" file : included all config info of all user and all repo in system
>- "~/.gitconfig" file : your personal and global git config info
>- ".git/config" file : config info of your git project

后期你也可以根据需要，时刻更新 git 配置信息 .

```bash
# add a new git config
$ git config --local/--global/--system --add section.key value
```

```bash
# delete the specifical git config
$ git config --local/--global/--system --unset section.key
```



## Branch

### 2.1 新建分支之前查看已有分支

通常情况下，我们会在 Clone 项目后修改项目配置或代码，或者想要修复项目 Bug 及完成新增需求，但在此操作之前，建议先在原有主分支的基础上新建分支，进而避免我们修改的操作会污染到主分支 .

但在新建分支之前，我们可能需要先检查本地分支有哪些 : 

```shell
# show the branches
$ git branch
# show the current branch
$ git branch --show-current
# show the all branches included the remote branches
$ git branch -a
```



## CheckOut

### 2.2 在主分支的基础上新建分支

在当前所在分支的上新建分支，然后切换到新建的分支 : 

```bash
$ git checkout -b new_branch
```



## Status

### 3.1  查看更新了那些文件

在新建的分支上更新一些文件后，你可能需要查看究竟更新了那些文件，这条命令主要为了查看当前 git 项目的状态，故适用于任何阶段 .

```bash
$ git status
```



## Diff

### 4.1  查看更新前后的对比信息

在将被修改的文件加入到本地仓库 Repository 之前，以防万一，你可能需要对比一下修改前后的信息，值得注意的是，如果新建的 git 项目，即 Index 中没有 commit 信息，此命令是没有任何输出结果的，因为它默认与最后一条 commit，即最新的代码状态信息做比较 . 同理，如果是新增文件，而不是去修改文件，此命令也是没有任何输出结果滴 .

```bash
$ git diff
```

当然，如果你想与指定的 commit 做比较，而不是默认的最新的 commit，你可以这样做 :

```bash
$ git diff [commit_hash_value]
```

对了，有时你可能需要比较两个 commit 之间的差别 : 

```bash
# Compare with commit_hash_value_1, Show the what updated in commit_hash_value_2
$ git diff [commit_hash_value_1] [commit_hash_value_2]
```

最后，再多说一句，如果被修改的文件已经被添加（Add）到了本地 Index（且 WorkSpace 中没有待被 add 的文件），此时如果想要查看 Index 与当前分支最新 commit 的信息对比，可以使用下述命令 : 

```bash
$ git diff [current_branch_name]
```



## Restore

### 5.1 撤销 WorkSpace 中更新的信息

如果你想废弃，即撤销在 WorkSpace 中已更新的信息，你可以使用 git 命令 `restore` .

```bash
# discard all the changes in working directory
$ git restore .
# discard the specifical changes in working directory
$ git restore [file_name_1] [file_name_2] [...]
```

但值得注意的是，如果是在 WorkSpace 中新增新文件，则无法使用 `restore` 命令来删除所新增的文件，如果想要删除所新增的文件，可以考虑下 Shell 命令 : `rm -rf`，或使用 `git reset` 命令 : 

```bash
# 硬重置： 将当前分支的 HEAD 重置到指定的提交，并且清除暂存区 Index 和工作目录 WorkSpace 的更改，使它们与指定的提交完全一致
git reset --hard <commit_hash_value>
```



## Add

### 6.1 将被更新的文件添加到本地 Index

知道自己在新建的分支上更新了哪些文件且核实无误后，你需要将被更新后的文件加入到本地 Index，以便于后续继续更新污染此次更新、及便于后续跟踪项目更新进度 .

```bash
# add the specifical files into repository
$ git add [file_name_1] [file_name_2] [...]
# add all updated files into repository
$ git add .
```

### 6.2  撤销已添加到 Index 中的文件

如果你后悔，你可以撤销已添加（add）到本地 Index 中的文件 :

```bash
# Restore all files
$ git restore --staged .
# Restore the specifical files
$ git restore --staged [file_name_1] [file_name_2] [...]
```



## Commit

### 7.1 将本地 Index 中的信息提交到本地 Repository

```bash
$ git commit -m 'add some comments for this commit'
```



## Log

### 7.2 查看本地 Repository 中的提交历史

`git log` 命令用于显示一个或多个分支的提交历史，这个命令可以帮助你查看项目的历史变更，包括每次提交的哈希值、作者、日期、提交信息等 .

简而言之，`git log` 命令主要用于帮助你深入了解项目的开发历史 .

```bash
$ git log
```



## Reflog

### 8.1 追踪和恢复 Git 仓库的历史状态

`git reflog` 命令主要用于查看和恢复Git仓库的历史引用记录，不同于`git log`，`git reflog`记录的是所有分支、标签、HEAD等引用的变化历史，包括每一次切换分支、修改、重置操作等 .

简而言之，`git reflog` 命令主要用于追踪和恢复 Git 仓库的历史状态，是进行 Git 操作时不可或缺的安全网 .

```bash
$ git reflog
```



## Merge

### 9.1 完成测试后切换到主分支

在实际项目开发流程中，我们一般会为了修复 bug 或完成新增需求，而在项目主分支上新建分支，这样做的目的是为了避免更新的不确定、是否安全的代码污染到已稳定运行的主分支（通常是指分支 Master / Main）代码，只有当我们充分测试新建分支的代码后，即确保将其合并（Merge）到主分支后不会造成更大的麻烦后，才会将其合并到主分支 .

首先我们从副分支（从主分支上新建的分支）切换到主分支 : 

```bash
$ git checkout master
```

### 9.2 将其它分支合并到主分支

切换到主分支（通常是指分支 Master / Main）后，然后我们将副分支（从主分支上新建的分支）合并到主分支 .

```bash
$ git merge [other_branch_copy_from_master]
```

对了，如果当你完成上述步骤后不再需要副分支，则可以将其删除 .

```bash
$ git branch -d [other_branch_copy_from_master]
```



## Pull

### 10.1 拉取远程 Repository 中的代码

如果远程主机的版本比本地版本更新，推送（Push）时 Git 会报错，要求先在本地做 `git pull` 合并差异，然后再推送到远程主机，git pull 的用法如下所示，值得注意的是 : `git pull` 命令等价于：先执行 `git fetch` 拉取远程分支更新的数据，再执行 `git merge FETCH_HEAD` 将远程仓库对应分支的最新提交合并到当前本地分支中 .

```bash
$ git pull [remote_branch_name] [loca_branch_name]
```

### 10.2 拉取过程中遇到的常见错误

但是但是但是，值得注意的是 : 当执行 `git push origin main` 命令后，可能会出现错误信息，大多数的错误原因是 : 这是由于你 pull 分支之前，进行过更新分支操作，而且其他人在你 pull 之前已经 push 过一个版本，进而导致本地与远程分支内容发生冲突 .

三种解决方案的命令如下 : 

```bash
# 这个命令将 pull.rebase 配置选项设置为 false，意味着当你执行 git pull 时，Git不会使用 rebase 方式来合并远程分支的更改。
# 相反，它会使用 merge 方式，这意味着 Git 将创建一个新的合并提交，这个提交将远程分支的最新状态与你的本地分支的当前状态合并在一起。
# 这种方式保留了历史的线性，使得你可以看到何时从远程分支拉取了更新。
$ git config pull.rebase false
```

```bash
# 这个命令将 pull.rebase 配置选项设置为 true，意味着当你执行 git pull 时，Git将使用 rebase 方式来合并远程分支的更改。
# 这意味着 Git 将重写你的本地提交历史，使其看起来像是你在远程分支的最新状态之后提交了你的更改。
# 这种方式可以保持你的提交历史更加整洁和平滑，但是它会改变提交的ID，可能会影响依赖于这些 ID 的外部引用。
$ git config pull.rebase true
```

```bash
# 这个命令将 pull.ff 配置选项设置为 only，意味着当你执行 git pull 时，Git只会在可以执行快速前进（Fast-forward，简称FF）合并时进行操作。
# 快速前进合并是指，当你的本地分支与远程分支的头部是直接的祖先关系时，可以简单地将远程分支的头部移动到你的本地分支的头部，而无需创建合并提交。
# 如果无法进行快速前进合并，git pull将拒绝执行，并提示你解决冲突或手动合并。
$ git config pull.ff only
```



## Push

### 11.1 将本地 Repository 推送到 Remote

下述命令将本地的 `master` 分支推送到 `origin` 主机，同时指定 `origin` 为默认主机，这样后面的 push 操作就可以不加任何参数地使用`git push`了 .

值得注意的是 : 在 Git 中，`origin` 是一个远程仓库的默认别名，当你克隆一个仓库时，Git 会自动创建一个远程仓库的引用，这个引用通常被称为 `origin`，这个别名指向的是你克隆的仓库的原始位置，也就是你的本地仓库与之同步的远程仓库，你可以通过 `git remote -v` 命令查看所有远程仓库的详细信息，包括 `origin` 的具体 URL .

```bash
$ git push -u origin master
```

如果远程主机的版本比本地版本更新，推送时 Git 会报错，要求先在本地做 `git pull` 合并差异，然后再推送到远程主机，这时，如果你一定要推送，可以使用 `--force / -f` 选项，此命令将强制用本地分支覆盖远程分支 .

```bash
$ git push --force/-f origin master
```



## Reset

### 12.1 将项目重置到某一个提交的状态

`git reset` 用于重置一个或多个文件到前一次提交的状态，或者将整个工作目录重置到某一个提交的状态，它可以用来撤销暂存区 Index 或工作目录 WorkSpace 的更改，或者重置当前分支的提交历史 .

```bash
# 混合重置（mixed 为默认选项）
# 将当前分支的 HEAD 重置到指定的提交，它会重置暂存区 Index，但工作目录 WorkSpace 中的更改会被保留
git reset --mixed <commit_hash_value>
```

```bash
# 软重置
# 将当前分支的 HEAD 重置到指定的提交，但保留暂存区 Index 和工作目录 WorkSpace 的更改
git reset --soft <commit_hash_value>
```

```bash
# 硬重置
# 将当前分支的 HEAD 重置到指定的提交，并且清除暂存区 Index 和工作目录 WorkSpace 的更改，使它们与指定的提交完全一致
git reset --hard <commit_hash_value>
```

值得注意的是 : 使用 `git reset` 需要额外小心，特别是 `--hard` 选项，因为它会永久删除未提交的更改，在使用 `git reset` 之前，最好确认你是否需要保存任何未提交的更改，如果需要，可以使用 `git stash` 保存更改，然后再进行重置操作 .




## Tag

### 13.1 标记软件发布点

在 Git 中，`tag` 是一种用于标记仓库中特定版本的引用，它们可以被看作是提交的命名标签，通常用于标记软件发布点，以下是 Git 中与`tag`相关的常见操作 : 

一. 查看所有标签 : 

```bash
$ git tag
```

二. 创建标签 : 

```bash
# 创建轻量级标签，如果不指定 commit_hash_value，则默认为当前 HEAD
$ git tag 'the_tag_name' [commit_hash_value]
```

```bash
# 创建带有注释的标签，其包含创建者、日期、描述性信息等
$ git tag -a 'the_tag_name' -m "the tag introduction..." [commit_hash_value]
```

三. 查看标签信息 : 

```bash
$ git show [tag_name]
```

四. 删除标签 : 

```bash
$ git tag -d [tag_name]
```

五. 推送标签到远程仓库 : 

```bash
# 推送单个标签
$ git push origin [tag_name]
```

```bash
# 推送所有标签
$ git push origin --tags
```

六. 更新标签 : 

值得注意的是，一旦创建了一个带有注释的标签，你不能直接编辑它，你需要删除旧标签，然后创建一个新的带有正确信息的标签，即你可以使用 `git tag -d [old_tag_name]` 删除旧标签，然后使用 `git tag [new_tag_name] [commit_hash_value]` 创建新标签指向相同的提交 .



## Archive

### 14.1 创建一个指定提交的归档文件

在 Git 中，`git archive` 命令用于创建一个指定提交的归档文件，通常是一个 tar 归档文件，但也可以是其他格式，如 zip，这个命令非常有用，尤其是在需要将一个项目的一个特定版本打包发送给其他人时 .以下是一些常见的 `git archive` 操作 : 

一. 创建不同格式的归档文件 : 值得注意的是，这里的 `HEAD` 指的是当前分支的最新提交 .

```bash
# 创建一个包含最新提交的 tar 归档文件
$ git archive --format=tar --output=archive.tar HEAD
```

```bash
# 创建一个包含最新提交的 zip 归档文件
$ git archive --format=zip --output=archive.zip HEAD
```

```bash
# 如果你想用其它格式，可以查看其所支持的所有可用格式
$ git archive --list
```

二. 从特定的提交创建归档 : 

```bash
$ git archive --format=zip --output=archive.zip [commit_hash_value]
```

三. 归档指定子目录 : 

```bash
# 例如只归档 test_folder 目录
$ git archive --format=zip --output=archive.zip HEAD test_folder
```

四. 解压归档文件 : 

你可以使用标准的解压缩工具来解压归档文件，例如使用 `tar` 或 `unzip` 命令 .



## Help

如果你想要查看全部 git 命令的使用文档，可以使用 `git --help`，当然，你也可以进一步查看指定 git 命令的详细使用文档 : `git [command] --help`



## 知识扩展

下面这三篇博客是我在 2019 年写的，现不知不觉已经 2024 年了，刚刚看了看自己的[博客网站](https://goog.tech/blog/)，莫名感动... 那时的自己真得很努力很努力... 谢谢自己！！！

* [GitHub中emoji表情的含义及使用规范](https://goog.tech/blog/2019/08/10/GitHub%E4%B8%ADemoji%E8%A1%A8%E6%83%85%E7%9A%84%E5%90%AB%E4%B9%89%E5%8F%8A%E4%BD%BF%E7%94%A8%E8%A7%84%E8%8C%83/)
* [Git Commit Message 规范](https://goog.tech/blog/2019/08/08/Git-Commit-Message-%E8%A7%84%E8%8C%83/)
* [理解 GitHub 的工作流程](https://goog.tech/blog/2019/07/16/Understanding-the-GitHub-flow/)
