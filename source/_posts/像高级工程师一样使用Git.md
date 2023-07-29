---
title: 像高级工程师一样使用Git
date: 发布时间 2023-7-30 00:06:33
categories:
  - 教程
tags:
  - Git
  - log
  - coding
  - 日志查看
  - 编程
description: 大家在使用Git中经常会查看Git日志，本文教大家正确有效的查看Git log。
cover: https://s2.loli.net/2023/07/29/XaJnWAPdmtSbreu.png
---

##浏览 Git 日志

默认情况下，Git 日志很难查看（不容易看懂，不容易找到症结），但是查看git log往往是最基本的。使用 git log 可以提供一些信息。但它的分辨性较低，通常不是您正在寻找的内容。
> ``` git log ```
![git log](https://s2.loli.net/2023/07/29/cmLtZQzvxbREH6U.png)
让我们保持真实。这些日志并没有给任何人留下深刻印象。它们很无聊。而且它们充满了您现在不需要的信息。您正在尝试对项目中发生的情况有一个高层次的了解。
有更好的方法。
带有更多可见性的 git 日志
使用 --graph 和 --format，我们可以快速获取项目中 git 提交的摘要视图。
git log --graph --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset) %C(white)%an%C(reset)%C(bold yellow)%d%C(reset) %C(dim white)- %s%C(reset)' --all

git log --graph --format=format:’<>’ --all

哇！这些日志看起来很不错！旁边甚至有一个分支树的样子。
这些日志向您展示了谁在做什么，何时进行了更改以及您的更改如何适应更大的情况。
--graph 在左侧添加了树形图。它不是最时尚的图形，但它有助于可视化项目分支中的更改。（ 在此处阅读文档。）
--format 允许您自定义日志的格式。有可供选择的预设格式，或者您可以编写自己的格式，例如此示例。（ 在此处阅读文档。）
--all 包括日志中的所有引用、标记和分支（包括远程分支）。您可能不想要所有内容，因此请根据需要进行调整。（ 在此处阅读文档。）
请参阅 git-log 文档，了解有关如何升级 git 日志的更多信息。→
理解特定提交
您经常希望了解特定提交发生了什么。git show 可以向您显示提交中更改的高级视图，但它还可以让您查看特定文件的更改。
查看提交摘要
git show <commit> --stat

git show <commit> --stat

使用 --stat 标志，您将看到提交摘要以及已更改的文件以及它们如何更改的详细信息。
查看提交的特定文件更改
当您想要深入了解特定文件中的特定行更改时，请使用带有文件路径的 git show。
git show <commit> -- <filepath>

git show <commit> -- <filepath>

这为您提供了文件的具体行更改。默认情况下，它将向您显示行更改以及文件中所更改行所在位置的另外 3 行上下文。
请参阅 git-show 文档，了解有关如何升级 git 提交理解的更多信息。→
进行更改
您已在项目上创建了一个分支，在分支上提交了一些更改，并准备将这些更改合并回主分支。由于您已经创建了分支，因此另一个工程师已对相同的文件进行了更改。 😱
如果您使用像 GitHub 这样的服务，则 PR 将告诉您是否存在合并冲突。

GitHub 合并冲突

Git 将提示您在将更改推回主分支之前解决这些合并冲突。这很好，因为您不想摧毁其他人正在进行的所有艰苦工作。
要开始本地解决此问题，通常会采取以下两种路径之一：合并或变基。
git merge vs git rebase
当主分支上有要合并到您的分支中的更改时，可以将这些更改合并或者将您的分支从不同点变基。
合并将一个分支中的更改合并到另一个分支中，生成一个合并提交。
git merge origin/main your-branch

变基会调整分支实际分支出去的点（即从基本分支开始移动分支到一个新起点）。
git rebase origin/main your-branch

通常，当上游分支（如 main）中存在更改时，您将使用变基来包含您的分支中。当您想将一个分支中的更改放回主分支时，将使用合并。
压缩还是不压缩
我曾经是压缩派。但 Derek Austin 博士的一篇文章 🥳 改变了我的看法。我推荐这篇文章，并认为自己没有任何有用的补充意见。
为什么我喜欢常规合并提交而不是压缩提交
我曾经认为压缩提交很酷，然后我不得不一天到晚使用它们。以下是避免使用压缩提交的原因...

betterprogramming.pub

资源
git-log 文档
git-show 文档
何时使用 Git 变基而不是 Git 合并？ (Stack Overflow)

您选择的文本
I’ve used these features of Git for years across teams and projects. I’m still developing opinions around some workflows (like to squash or not) but the core tooling is powerful and flexible (and scriptable!). Going through Git logs
您希望对文本进行什么操作？

将这段文字翻译成 Chinese (Simplified)。

多年来，我一直在团队和项目中使用 Git 的这些功能。我仍在开发某些工作流程的意见（例如压缩或不压缩），但核心工具是强大且灵活的（并且可脚本化！）。
浏览 Git 日志
