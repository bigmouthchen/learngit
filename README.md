﻿# Git 学习笔记

标签： Git

---
参考链接：  [progit-zh](https://git-scm.com/book/zh/v2)   [廖雪峰的Git教程](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

Git是目前世界上最先进的分布式版本控制系统。那么什么版本控制？

## 关于版本控制
版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。有了它你就可以将某个文件回溯到之前的状态，甚至将整个项目都回退到过去某个时间点的状态，你可以比较文件的变化细节，查出最后是谁修改了哪个地方，从而找出导致怪异问题出现的原因，又是谁在何时报告了某个功能缺陷等等。 使用版本控制系统通常还意味着，就算你乱来一气把整个项目中的文件改的改删的删，你也照样可以轻松恢复到原先的样子。 但额外增加的工作量却微乎其微。

## Git 的诞生
Linux 内核开源项目有着为数众广的参与者。绝大多数的 Linux 内核维护工作都花在了提交补丁和保存归档的繁琐事务上（1991－2002 年间）。到 2002 年，整个项目组开始启用一个专有的分布式版本控制系统 BitKeeper 来管理和维护代码。

到了 2005 年，开发 BitKeeper 的商业公司同 Linux 内核开源社区的合作关系结束，他们收回了 Linux 内核社区免费使用 BitKeeper 的权力。 这就迫使 Linux 开源社区（特别是 Linux 的缔造者 Linux Torvalds）基于使用 BitKcheper时的经验教训，开发出自己的版本系统。 他们对新的系统制订了若干目标：

- 速度
- 简单的设计
- 对非线性开发模式的强力支持（允许成千上万个并行开发的分支）
- 完全分布式
- 有能力高效管理类似 Linux 内核一样的超大规模项目（速度和数据量）

## Git 基础

### 直接记录快照，而非差异比较
Git 和其它版本控制系统的主要差别在于 Git 对待数据的方法。概念上来区分，其它大部分系统以文件变更列表的方式存储信息。这类系统将它们保存的信息看作是一组基本文件和每个文件随时间逐步累积的差异。Git 不按照以上方式对待或保存数据。 反之，Git 更像是把数据看作是对小型文件系统的一组快照。 每次你提交更新，或在 Git 中保存项目状态时，它主要对当时的全部文件制作一个快照并保存这个快照的索引。 为了高效，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。 Git 对待数据更像是一个 快照流。

### 近乎所有操作都是本地执行
在 Git 中的绝大多数操作都只需要访问本地文件和资源，一般不需要来自网络上其它计算机的信息。

### Git 保证完整性
Git 中所有数据在存储前都计算校验和，然后以校验和来引用。 这意味着不可能在 Git 不知情时更改任何文件内容或目录内容。 这个功能建构在 Git 底层，是构成 Git 哲学不可或缺的部分。 若你在传送过程中丢失信息或损坏文件，Git 就能发现。

Git 用以计算校验和的机制叫做 SHA-1 散列（hash，哈希）。 这是一个由 40 个十六进制字符（0-9 和 a-f）组成字符串，基于 Git 中文件的内容或目录结构计算出来。 SHA-1 哈希看起来是这样：
    
    24b9da6552252987aa493b52f8696cd6d3b00373
实际上，Git 数据库中保存的信息都是以文件内容的哈希值来索引，而不是文件名。

### Git 一般只添加数据
你执行的 Git 操作，几乎只往 Git 数据库中增加数据。 很难让 Git 执行任何不可逆操作，或者让它以任何方式清除数据。 同别的 VCS 一样，未提交更新时有可能丢失或弄乱修改的内容；但是一旦你提交快照到 Git 中，就难以再丢失数据，特别是如果你定期的推送数据库到其它仓库的话。

### 三种状态
Git 有三种状态，你的文件可能处于其中之一：已提交（committed）、已修改（modified）和已暂存（staged）。 已提交表示数据已经安全的保存在本地数据库中。 已修改表示修改了文件，但还没保存到数据库中。 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中。

由此引入 Git 项目的三个工作区域的概念：Git 仓库、工作目录以及暂存区域。

Git 仓库目录是 Git 用来保存项目的元数据和对象数据库的地方。 这是 Git 中最重要的部分，从其它计算机克隆仓库时，拷贝的就是这里的数据。

工作目录是对项目的某个版本独立提取出来的内容。 这些从 Git 仓库的压缩数据库中提取出来的文件，放在磁盘上供你使用或修改。

暂存区域是一个文件，保存了下次将提交的文件列表信息，一般在 Git 仓库目录中。 有时候也被称作“索引”，不过一般说法还是叫暂存区域。

基本的 Git 工作流程如下：

1. 在工作目录中修改文件。

2. 暂存文件，将文件的快照放入暂存区域。

3. 提交更新，找到暂存区域的文件，将快照永久性存储到 Git 仓库目录。

如果 Git 目录中保存着的特定版本文件，就属于已提交状态。 如果作了修改并已放入暂存区域，就属于已暂存状态。 如果自上次取出后，作了修改但还没有放到暂存区域，就是已修改状态。

## 在Windows上安装 Git
下载地址：
1. [官方](https://git-scm.com/download/win) 
2. [https://git-for-windows.github.io](https://git-for-windows.github.io)

## 配置 Git

### 配置用户信息
首次使用时配置如下，在 Git Bash 命令行中输入：
    
    $ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"

如果使用了 `--global` 选项，那么该命令只需要运行一次，因为之后无论你在该系统上做任何事情， Git 都会使用那些信息。 当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运行没有 `--global` 选项的命令来配置

### 配置文本编辑器
使用以下代码配置你的文本编辑器

    $ git config --global core.editor "C:\Software\Sublime Text 3\sublime_text.exe"

如果未配置，Git 会使用操作系统默认的文本编辑器，在 windows 上就是记事本，出于种种原因，使用记事本编辑的代码可能会遇到很多不可思议的问题，建议使用其他文本编辑器替代。

### 检查配置信息
如果想要检查你的配置，可以使用 `git config --list` 命令来列出所有 Git 当时能找到的配置。

    $ git config --list
    user.name=John Doe
    user.email=johndoe@example.com
    color.status=auto
    color.branch=auto
    color.interactive=auto
    color.diff=auto
    ...
    
你可以通过输入 `git config <key>` 来检查 Git 的某一项配置

    $ git config user.name
    John Doe

## 获取帮助
若你使用 Git 时需要获取帮助，有三种方法可以找到 Git 命令的使用手册：

    $ git help <verb>
$ git <verb> --help
    $ man git-<verb>
例如，要想获得 config 命令的手册，执行

    $ git help config

这些命令很棒，因为你随时随地可以使用而无需联网。如果你觉得手册或者本书的内容还不够用，你可以尝试在 Freenode IRC 服务器（ irc.freenode.net ）的 #git 或 #github 频道寻求帮助。 这些频道经常有上百人在线，他们都精通 Git 并且乐于助人。

## 获取 Git 仓库
有两种取得 Git 项目仓库的方法。第一种是在现有项目或目录下导入所有文件到 Git 中； 第二种是从一个服务器克隆一个现有的 Git 仓库。

### 在现有目录中初始化仓库
首先，选择一个合适的地方，创建一个空目录，并在Git Bash中进入该目录（类似于 windows 上 cmd 中的操作），通过 `git init` 命令把这个目录变成 Git 可以管理的仓库.

    $ cd e:\mygit
$ git init
    Initialized empty Git repository in E:/mygit/.git/
    
然后你就会发现当前目录下多了一个.git的目录，如果你没有看到.git目录，那是因为这个目录默认是隐藏的，在文件夹选项中修改一下文件夹可见属性就行了。这个目录是Git来跟踪管理版本库的，没事千万不要手动修改这个目录里面的文件，不然改乱了，就把Git仓库给破坏了。

如果你是在一个已经存在文件的文件夹（而不是空文件夹）中初始化 Git 仓库来进行版本控制的话，你应该开始跟踪这些文件并提交。 你可通过 git add 命令来实现对指定文件的跟踪，然后执行 git commit 提交：

    $ git add *.c
$ git add LICENSE
    $ git commit -m 'initial project version'
    
`git commit` 命令，`-m` 后面输入的是本次提交的说明，这样你就能从历史记录里方便地找到改动记录。`commit` 可以一次提交很多文件，所以你可以多次 `add` 不同的文件

### 克隆现有的仓库
如果你想获得一份已经存在了的 Git 仓库的拷贝，比如说，你想为某个开源项目贡献自己的一份力，这时就要用到 `git clone` 命令。克隆仓库的命令格式是 `git clone [url]` 。比如：

    $ git clone https://github.com/libgit2/libgit2

这会在当前目录下创建一个名为 “libgit2” 的目录，如果你想在克隆远程仓库的时候，自定义本地仓库的名字，你可以使用如下命令：

    $ git clone https://github.com/libgit2/libgit2 mylibgit

这将执行与上一个命令相同的操作，不过在本地创建的仓库名字变为 mylibgit。


