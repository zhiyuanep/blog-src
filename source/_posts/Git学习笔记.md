---
title: Git学习笔记
date: 2017-04-19 22:57:26
tags:
 - Git
 - 学习笔记
categories:
 - Git
---

摘录于《Git Pro》。原文地址:https://git-scm.com/book/zh/v2/

## 背景知识 ##

### 关于版本控制 ###

#### 什么是“版本控制”？ ####

版本控制是一种记录一个或若干文件内容变化，以便将来查阅特定版本修订情况的系统。

#### 版本控制系统(VCS)的演进 ####

本地版本控制系统 -> 集中化的版本控制系统 -> 分布式版本控制系统。

#### 本地版本控制系统 ####

大多数都是采用某种简单的数据库来记录文件的历次更新的差异。其中最流行的是RCS，它的工作原理是在硬盘上保存补丁集（文件修订前后的变化）；通过应用所有补丁，可以重新计算出各个版本的文件内容。

{% asset_img local.png 本地版本控制系统 %}

#### 集中化的版本控制系统(CVCS) ####

是为了让不同系统上的开发者协同工作而生。些类系统包括 CVS、Subversion(SVN) 以及 Perforce 等。都有一个单一的集中管理的服务器，保存所有文件的修订版本。客户端需要连接到该服务器才能取出最新的文件或者提交更新。

{% asset_img centralized.png 集中化版本控制系统 %}

相比于本地 VCS 来说，可以在一定程度上看到项目中其它人的工作成果。而管理员也可以轻松掌控每个开发者的权限。其显而易见的缺点是中央服务器的单点故障。如果服务器宕机，所有人都无法更新和提交。如果服务器磁盘发生损坏，则会丢失项目整个变更历史，只剩下每个人各自机器上保留的单独快照。本地 VCS 也存在该问题。此外，客户端必须与服务器联网才能更新和提交。

#### 分布式版本控制系统 ####

分布式版本控制系统 (DVCS) 是为了解决上述问题而生。这类系统包括 Git、Mercurial、Bazaar 以及 Darcs 等。客户端不再只是提取最新版本的文件快照，而是把代码仓库完整地镜像下来。任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复。

{% asset_img distributed.png 分布式版本控制系统 %}

更进一步，许多这类系统都可以指定和若干不同的远端代码仓库进行交互。籍此，你就可以在同一个项目中，分别和不同工作小组的人相互协作。

### Git 的思想和基本工作原理 ###

注意与 Subversion 等其它版本管理系统的区别。

#### 直接记录快照，而非差异比较 ####

Git 的这种对待数据的方法是与其它版本控制系统（包括 Subversion 和近似工具）的主要差别。

其它大部分系统以文件变更列表的方式存储信息，它们将保存的信息看作是一组**基本文件**和每个文件随着时间逐步累积的**差异**。

{% asset_img deltas.png 存储每个文件及与初始版本的差异 %}

Git 将数据看作是对小型文件系统的一组快照。每次提交更新或在 Git 中保存项目状态时，它主要对当时的全部文件制作一个快照并保存这个快照的索引。如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。Git 对待数据更像是一个 **快照流**。

{% asset_img snapshots.png 存储项目随时间改变的快照 %}

#### 近乎所有操作都是本地执行 ####

Git 中绝大多数操作都只需要访问本地文件和资源，一般不需要来自网络上其它计算机的信息。比如你要浏览项目的历史，因为你的本地磁盘保存的是完整的代码库，你可以随时随地查看项目历史而无需与服务器联网，这一点在以往的集中式版本控制系统无法做到。如果你需要提交修改，在不联网的情况下，Git 允许你先提交到本地仓库，待联网后再推送到远程仓库。而而集中式版本控制系统（比如 Subversion）必须与服务器处于联网状态才能够提交修改。

#### Git 保证完整性 ####

Git 中所有数据在存储前都计算校验和，然后以校验和来引用。 这意味着不可能在 Git 不知情时更改任何文件内容或目录内容。 这个功能建构在 Git 底层，是构成 Git 哲学不可或缺的部分。 若你在传送过程中丢失信息或损坏文件，Git 就能发现。

#### Git 一般只添加数据 ####

你执行的 Git 操作，几乎都是只往 Git 数据库中增加数据。很难让 Git 执行任何不可逆操作。或者让它以任何方式清除数据。未提交更新时有可能丢失或弄乱修改的内容，但是一旦提交快照到 Git 中，就很难再丢失数据。

#### 三种状态 ####

Git 有三种状态，你的文件可能处于其中之一：已修改（modified）、已暂存（staged）、已提交（committed）。已修改表示修改了文件，但还没保存到数据库中；已暂存表示对一个已修改文件的当前版本作了标记，使之包含在下次提交的快照中；已提交表示数据已经安全保存在本地数据库中。

由三种状态引入 Git 项目的三个工作区域的概念：工作目录、暂存区域、Git 仓库。

{% asset_img areas.png 工作目录、暂存区域以及 Git 仓库 %}

工作目录：是对项目的某个版本独立提取出来的内容。这些从 Git 仓库的压缩数据库中提取出来的文件，放在磁盘上供你使用或修改。
暂存区域：是一个文件，保存了下次将提交的文件列表信息，一般在 Git 仓库目录中。
Git 仓库：是 Git 用来保存项目的元数据和对象数据库的地方。这是 Git 中最重要的部分，从其它计算机克隆仓库时，拷贝的就是这里的数据。

#### 基本的 Git 工作流程 ####

1. 在工作目录中修改文件；
2. 暂存文件，将文件的快照放入暂存区域。
3. 提交更新，找到暂存区域的文件，将快照永久性存到 Git 仓库目录。

如果自上次取出后，作了修改但还没有放到暂存区域，就是已修改状态；如果作为修改并已放入暂存区域，就属于已暂存状态；如果 Git 目录中保存着特定版本文件，就属于已提交状态。

### 初次运行 Git 前的配置 ###

Git 自带一个 `git config` 的工具来帮助设置控制 Git 外观和行为的配置变量。这些变量配置文件在三个地方：

1. `/etc/gitconfig`：包含系统上每一个用户及他们仓库的通用配置。如果使用带有 `--system` 选项的 `git config` 时，它会从此文件读写配置变量。
2. `~/.gitconfig`或 `~/.config/git/config`文件：只针对当前用户。可以传递 `--global` 选项让 Git 读写些文件。
3. 当前使用仓库的 Git 目录中的 `config` 文件（就是`.git/config`）：针对该仓库。

每个具体级别覆盖上一级别的配置。

#### 用户信息 ####

设置用户名称和邮件地址，每一个 Git 的提交都会使用这些信息，并且会写入到每一次提交中，不可更改：

    $ git config --global user.name "John Doe"
    $ git config --global user.email "johndoe@example.com"
    
注意：如果使用了`--global`选项，那么对于该用户上的所有项目都默认使用这些信息。如果想针对特定项目使用不同的用户名称与邮件地址，可以在该项目目录下运行没有`--global`选项的命令来配置。

#### 文本编辑器 ####

当 Git 需要你输入信息时会调用它。如果未配置，Git 会使用操作系统默认的文本编辑器。如果想使用不同的文本编辑器，可以这样做：

    $ git config --global core.editor emacs

#### 检查配置信息 ####

可以使用 `git config --list`命令来列出所有 Git 当时能找到的配置。

可能会看到重复的变量名，因为 Git 会从不同的文件中读取同一个配置。这种情况下，Git 会使用它找到的每一个变量的最后一个配置。

可以通过输入`git config <key>`来检查特定配置项。

    $ git config user.name
    John Doe
    
### 获取帮助 ###

有三种方法可以找到 Git 命令的使用手册：

    $ git help <verb>
    $ git <verb> --help
    $ man git-<verb>


## Git 基础 ##

### 获取 Git 仓库 ###

有两种方法：第一种是在现有项目或目录下导入所有文件到 Git 中；第二种是从一个服务器克隆一个现有的 Git 仓库。

#### 在现有目录中初始化仓库 ####

进入现有的项目目录，并输入：

    $ git init

该命令会创建一个名为 `.git` 的子目录。该命令仅仅是作了一个初始化的操作，项目里的文件还没有被跟踪。

如果是在一个已经存在文件的文件夹中初始化 Git 仓库来进行版本控制的话，可通过 `git add`命令来实现对指定文件的跟踪，然后执行`git commit`提交。例如：

    $ git add *.c
    $ git add LICENSE
    $ git commit -m 'initial project version'

#### 克隆现有的仓库 ####

可以使用`git clone`命令。克隆该 Git 仓库服务器上的几乎所有数据，而不是仅仅复制完成你的工作所需要的文件。默认配置下远程 Git 仓库的每一个文件的每一个版本都将被拉取下来。如果服务器的磁盘坏掉了，可以使用任何一个克隆下来的用户端来重建服务器上的仓库。

比如要克隆 Git 的可链接库 libgit2，可以用下面的命令：

    $ git clone https://github.com/libgit2/libgit2

如果想在克隆远程仓库的时候，自定义本地仓库的名字，可以使用如下命令：

    $ git clone https://github.com/libgit2/libgit2 mylibgit

这样，本地创建的仓库名字变为 `mylibgit`。

Git 支持多种数据传输协议。 上面的例子使用的是 `https://` 协议，不过你也可以使用 `git://` 协议或者使用 SSH 传输协议，比如 `user@server:path/to/repo.git` 。

### 记录每次更新到仓库 ###

工作目录下的每个文件不外乎两种状态：已跟踪或未跟踪。
已跟踪：指那些被纳入版本控制的文件，在上一次快照中有它们的记录。
未跟踪：工作目录中除已跟踪文件以外的所有其它文件都属于未跟踪文件。它们既不存在于上次快照中，也没有放入暂存区。
初次克隆某个仓库的时候，工作目录的所有文件都属于已跟踪文件，并处于未修改状态。

编辑过某些文件后，Git 将它们标记为已修改文件。再将这些修改过的文件入入暂存区，然后提交所有暂存了的修改，如此反复。

{% asset_img lifecycle.png 文件状态变化周期 %}

#### 检查当前文件状态 ####

要查看哪些文件处于什么状态，可以用 `git status`命令。

如果在克隆仓库后立即使用此命令，会看到类似这样的输出：

    $ git status
    On branch master
    nothing to commit, working directory clean

说明现在的工作目录相当干净，所有已跟踪文件在上次提交后都未被更改过。

在项目下创建一个新的 README 文件，如果之前不存在这个文件，使用`git status`命令，你将看到一个新的未跟踪文件：

    $ echo 'My Project' > README
    $ git status
    On branch master
    Untracked files:
      (use "git add <file>..." to include in what will be committed)

        README

    nothing added to commit but untracked files present (use "git add" to track)

在状态报告中可以看到新建的 README 文件出现在 `Untracked files`下面。未跟踪的文件意味着 Git 在之前的快照（提交）中没有这些该文，Git 不会自动将之纳入跟踪范围。除非你明明白白告诉它“我需要跟踪该文件”。

#### 跟踪新文件 ####

使用命令 `git add`开始跟踪文件。要跟踪 README 文件，运行：

    $ git add README

再运行`git status`命令，会看到 README 文件已经被跟踪，并处于暂存状态：

    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

        new file:   README

只要在`Changes to be committed`这行下面的，就说明是已暂存状态。`git add`命令使用文件或目录的路径作为参数，如果参数是目录的路径，该命令将递归地跟踪该目录下的所有文件。

#### 暂存已修改文件 ####

如果你修改了一个名为 `CONTRIBUTING.md`的已被跟踪的文件，再运行`git status`命令，会看到下面内容：

    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

        new file:   README

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   CONTRIBUTING.md

可以看到`CONTRIBUTING.md`文件已经被修改，且并未放到暂存区。要暂存这次更新，需要运行 `git add`命令。这是个多功能命令：可以用它开始跟踪新文件，或者把被修改的已跟踪文件放到暂存区，还能用于合并时把有冲突的文件标记为已解决状态等。

将`git add`命令理解为“添加内容到下一次提交中”而不是“将一个文件添加到项目中”更加合适。

暂存`CONTRIBUTING.md`之后再看看`git status`的输出：

    $ git add CONTRIBUTING.md
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

        new file:   README
        modified:   CONTRIBUTING.md、

如果你想再修改`CONTRIBUTING.md`文件，比如加一条注释，重新编辑存盘后，准备好提交。不过且慢，再运行`git status`看看：

    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

        new file:   README
        modified:   CONTRIBUTING.md

    Changes not staged for commit:
      (use "git add <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   CONTRIBUTING.md

可以看到`CONTRIBUTING.md`同时出现在暂存区和非暂存区。这是因为 Git 只不过暂存了你运行`git add`命令时的版本，如果你现在提交，`CONTRIBUTING.md`的版本是你最后一次运行`git add`命令时的那个版本，而不是你运行`git commit`时，在工作目录的当前版本。所以，运行了`git add`之后又作为修订的文件，需要重新运行`git add`把最新版本重新暂存起来：

    $ git add CONTRIBUTING.md
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

        new file:   README
        modified:   CONTRIBUTING.md

#### 状态简览 ####

`git status`命令输出十分详细，可以使用 `git status -s`，你将得到一种更为紧凑的格式输出：

    $ git status -s
     M README
    MM Rakefile
    A  lib/git.rb
    M  lib/simplegit.rb
    ?? LICENSE.txt

新添加的末跟踪文件前面有`??`标记，新添加到暂存区中的文件前面有`A`标记，修改过的文件前面有`M`标记。`M`有两个可以出现的位置，出现在右边的`M`表示该文件被修改了但是还没放入区，出现在靠左边的`M`表示该文件被修改了并放入了暂存区。`Rakefile` 前面有两个位置都出现了`M`，表示该文件修改了提交暂存区后又再次被修改。

#### 忽略文件 ####

一些自动生成的文件，比如日志文件，或者编译过程中创建的临时文件，无需纳入 Git 的管理，也不希望它们总出现在末跟踪文件列表。可以创建一个名为`.gitignore`的文件，列出要忽略的文件模式，例如：

    $ cat .gitignore
    *.[oa]
    *~

表示告诉 Git 忽略所有以 `.o` 或 `.a`结尾的文件，以用以波浪符(~)结尾的文件。

文件`.gitignore`的格式规范如下：

* 所有空行或者以`#`开头的行都会被 Git 忽略。
* 可以使用标准的 glob 模式匹配。
* 匹配模式可以以（`/`）开头防止递归。
* 匹配模式可以以（`/`）结尾指定目录。
* 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（`!`）取反。

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（`*`）匹配零个或多个任意字符；`[abc]` 匹配任何一个列在方括号中的字符（这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）；问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符，表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。 使用两个星号（`*`） 表示匹配任意中间目录，比如`a/**/z` 可以匹配 a/z, a/b/z 或 `a/b/c/z`等。

#### 查看已暂存和未暂存的修改 ####

使用`git diff`命令，将通过文件补丁的格式显示具体哪些行发生了改变。

要查看尚未暂存的文件更新了哪些部分，不加参数直接输入`git diff`：

    $ git diff
    diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
    index 8ebb991..643e24f 100644
    --- a/CONTRIBUTING.md
    +++ b/CONTRIBUTING.md
    @@ -65,7 +65,8 @@ branch directly, things can get messy.
     Please include a nice description of your changes when you submit your PR;
     if we have to read the whole diff to figure out why you're contributing
     in the first place, you're less likely to get feedback and have your change
    -merged in.
    +merged in. Also, split your changes into comprehensive chunks if your patch is
    +longer than a dozen lines.

     If you are starting to work on a particular area, feel free to submit a PR
     that highlights your work in progress (and note in the PR title that it's
     
此命令比较的是工作目录中当前文件和暂存区快照之间的差异，也就是修改之后不穿堂风有暂存起来的变化内容。

要查看已暂存的将要添加到下次提交里的内容，可以用`git diff --cached`或`git diff --staged`命令。

    $ git diff --cached
    diff --git a/CONTRIBUTING.md b/CONTRIBUTING.md
    index 8ebb991..643e24f 100644
    --- a/CONTRIBUTING.md
    +++ b/CONTRIBUTING.md
    @@ -65,7 +65,8 @@ branch directly, things can get messy.
     Please include a nice description of your changes when you submit your PR;
     if we have to read the whole diff to figure out why you're contributing
     in the first place, you're less likely to get feedback and have your change
    -merged in.
    +merged in. Also, split your changes into comprehensive chunks if your patch is
    +longer than a dozen lines.

     If you are starting to work on a particular area, feel free to submit a PR
     that highlights your work in progress (and note in the PR title that it's

#### 提交更新 ####

在提交更新之前，请务必先用`git status`看下，是不是所有新建或修改过的文件都已经暂存起来了，然后再运行提交命令`git commit`：

    $ git commit

这种方式会启动文本编辑器以便输入本次提交的说明 。默认的提交消息包含最后一次运行`git status`的输出，放在注释行里，另外开头还有一空行，供你输入提交说明。如果想要更详细的对修改了哪些内容的提示，可以用`-v`选项，这会将你所做的改变的diff输出放到编辑器中。退出编辑器时，Git 会丢掉注释行，用你输入提交附带信息生成一次提交。

可以在`git commit`命令后添加`-m`选项，将提交信息与命令放在同一行，如下所示：

    $ git commit -m "Story 182: Fix benchmarks for speed"
    [master 463dc4f] Story 182: Fix benchmarks for speed
     2 files changed, 2 insertions(+)
     create mode 100644 README

提交结果会告诉你，当前是哪个分支提交的，本次提交的完整SHA-1校验和是什么，以及在本次提交中有多少文件修订过，多少行添加和删改过。

每次提交都是对项目作一次快照，以后可以回到这个状态，或者进行比较。

#### 跳过使用暂存区域 ####

Git 提供了一个跳过使用暂存区域的方式，只要在提交的时候，给`git commit`加上`-a`选项，Git 就会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过`git add`步骤。

#### 移除文件 ####

要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除。可以用`git rm`命令完成些项工作，并连带从工作目录中删除指定的文件，这样以后就不会出现在末跟踪文件清单中了。

如果只是简单地从工作目录中手工删除文件，运行`git status`时就会在未暂存清单看到：

    $ rm PROJECTS.md
    $ git status
    On branch master
    Your branch is up-to-date with 'origin/master'.
    Changes not staged for commit:
      (use "git add/rm <file>..." to update what will be committed)
      (use "git checkout -- <file>..." to discard changes in working directory)

            deleted:    PROJECTS.md

    no changes added to commit (use "git add" and/or "git commit -a")

然后再运行`git rm`记录此次移除文件的操作：

    $ git rm PROJECTS.md
    rm 'PROJECTS.md'
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

        deleted:    PROJECTS.md

下一次提交时，该文件就不再纳入版本管理了。

如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项`-f`。这是一种安全特性，用于防止误删还没有添加到快照的数据，这样的数据不能被 Git 恢复。

另外一种情况是，我们想把文件从 Git 仓库中删除（变即从暂存区域移除），但仍然希望保留在当前工作目录中。换句话说，你想让文件保留在磁盘，但是并不想让 Git 继续跟踪。为达到这一目的，使用`--cached`选项。例如：

    $ git rm --cached README

`git rm`命令后面可以列出文件或者目录的名字，也可以使用`glob`模式，比方说：

    $ git rm log/\*.log

注意到星号`*`之前的反斜杠`\`，因为 Git 有它自己的文件模式扩展匹配方式，所以我们不用 shell 来帮忙展开。此命令删除`log/`目录下扩展名为`.log`的所有文件。

#### 移动文件 ####

Git 并不显式跟踪文件移动操作。如果在 Git 中重命名了某个文件，仓库中存储的元数据并不会体现出这是一次改名操作。

要在 Git 中对文件改名，可以这么做：

    $ git mv file_from file_to

查看状态信息，也是明白无误地看到关于重命名操作的说明：

    $ git mv README.md README
    $ git status
    On branch master
    Changes to be committed:
      (use "git reset HEAD <file>..." to unstage)

        renamed:    README.md -> README

其实，运行`git mv`就相当于运行了下面三条命令：

    $ mv README.md README
    $ git rm README.md
    $ git add README

如此分开操作，Git 也会意识到这是一次改名，所以不管何种方式结果都一样。两者唯一的区别是，`mv`是一条命令，而另一种方式需要三条命令，直接用`git mv`轻便得多。

### 查看提交历史 ###

要回顾提交历史，可以使用`git log`命令，默认不用任何参数的话，类似以下输出：

    $ git log
    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Mon Mar 17 21:52:11 2008 -0700

        changed the version number

    commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 16:40:33 2008 -0700

        removed unnecessary test

    commit a11bef06a3f659402fe7563abf99ad00de2209e6
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 10:31:28 2008 -0700

        first commit

`git log`会按提交时间列出所有的更新，最近的更新排在最上面，并列出每个提交的 SHA-1 校验和、作者的名字和电子邮件地址、提交时间以及提交说明。

#### 显示每交提交内容差异 ####

一个常用的选项是`-p`，用来显示每次提交的内容差异。你也可以加上`-2`来仅显示最近两次提交：

    $ git log -p -2
    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Mon Mar 17 21:52:11 2008 -0700

        changed the version number

    diff --git a/Rakefile b/Rakefile
    index a874b73..8f94139 100644
    --- a/Rakefile
    +++ b/Rakefile
    @@ -5,7 +5,7 @@ require 'rake/gempackagetask'
     spec = Gem::Specification.new do |s|
         s.platform  =   Gem::Platform::RUBY
         s.name      =   "simplegit"
    -    s.version   =   "0.1.0"
    +    s.version   =   "0.1.1"
         s.author    =   "Scott Chacon"
         s.email     =   "schacon@gee-mail.com"
         s.summary   =   "A simple gem for using Git in Ruby code."

    commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 16:40:33 2008 -0700

        removed unnecessary test

    diff --git a/lib/simplegit.rb b/lib/simplegit.rb
    index a0a60ae..47c6340 100644
    --- a/lib/simplegit.rb
    +++ b/lib/simplegit.rb
    @@ -18,8 +18,3 @@ class SimpleGit
         end

     end
    -
    -if $0 == __FILE__
    -  git = SimpleGit.new
    -  puts git.show
    -end
    \ No newline at end of file

当进行代码审查，或者快速浏览某个搭档提交的 commit 所带来的变化的时候，这个参数就非常有用了。

#### 查看提交统计信息 ####

可以使用`--stat`选项查看每次提交的简略的统计信息：

    $ git log --stat
    commit ca82a6dff817ec66f44342007202690a93763949
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Mon Mar 17 21:52:11 2008 -0700

        changed the version number

     Rakefile | 2 +-
     1 file changed, 1 insertion(+), 1 deletion(-)

    commit 085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 16:40:33 2008 -0700

        removed unnecessary test

     lib/simplegit.rb | 5 -----
     1 file changed, 5 deletions(-)

    commit a11bef06a3f659402fe7563abf99ad00de2209e6
    Author: Scott Chacon <schacon@gee-mail.com>
    Date:   Sat Mar 15 10:31:28 2008 -0700

        first commit

     README           |  6 ++++++
     Rakefile         | 23 +++++++++++++++++++++++
     lib/simplegit.rb | 25 +++++++++++++++++++++++++
     3 files changed, 54 insertions(+)

`--stat`选项在每次提交的下面列出所有被修改过的文件、有多少文件被修改了以及被修改过的文件的哪些行被移除或是添加了。在每次提交的最后还有一个总结。

#### 定制输出格式和内容 ####

另外一个常用的选项是`--pretty`。这个选项可以指定使用不同于默认格式的方式展示提交历史。这个选项有一些内建的子选项供你使用。比如用`oneline`将每个提交放在一行显示，查看的提交数很大时非常有用。另外还有`short`，`full`和`fuller`可以用，展示的信息或多或少有些不同。

    $ git log --pretty=oneline
    ca82a6dff817ec66f44342007202690a93763949 changed the version number
    085bb3bcb608e1e8451d4b2432f8ecbe6306e7e7 removed unnecessary test
    a11bef06a3f659402fe7563abf99ad00de2209e6 first commit

`format`可以定制要显示的记录格式。这样的输出对后期提取分析格外有用，因为你知道输出的格式不会随着 Git 的更新而发生改变：

    $ git log --pretty=format:"%h - %an, %ar : %s"
    ca82a6d - Scott Chacon, 6 years ago : changed the version number
    085bb3b - Scott Chacon, 6 years ago : removed unnecessary test
    a11bef0 - Scott Chacon, 6 years ago : first commit

下面的表格列出了`git log --pretty=format`常用的格式点位符写法及其代表的意义。

|选项|说明|
|:--|:--|
|`%H`|提交对象（commit）的完整哈希字串|
|`%h`|提交对象的简短哈希字串|
|`%T`|树对象（tree）的完整哈希字串|
|`%t`|树对象的简短哈希字串|
|`%P`|父对象（parent）的完整哈希字串|
|`%p`|父对象的简短哈希字串|
|`%an`|作者（author）的名字|
|`%ae`|作者的电子邮件地址|
|`%ad`|作者修订日期（可以用 --date= 选项定制格式）|
|`%ar`|作者修订日期，按多久以前的方式显示|
|`%cn`|提交者（committer）的名字|
|`%ce`|提交者的电子邮件地址|
|`%cd`|提交日期|
|`%cr`|提交日期，按多久以前的方式显示|
|`%s`|提交说明|

以下是`git log`的常用选项。

|选项|说明|
|:--|:--|
|-p|按补丁格式显示每个更新之间的差异。|
|--stat|显示每次更新的文件修改统计信息。|
|--shortstat|只显示 --stat 中最后的行数修改添加移除统计。|
|--name-only|仅在提交信息后显示已修改的文件清单。|
|--name-status|显示新增、修改、删除的文件清单。|
|--abbrev-commit|仅显示 SHA-1 的前几个字符，而非所有的 40 个字符。|
|--relative-date|使用较短的相对时间显示（比如，“2 weeks ago”）。|
|--graph|显示 ASCII 图形表示的分支合并历史。|
|--pretty|使用其他格式显示历史提交信息。可用的选项包括 oneline，short，full，fuller 和 format（后跟指定格式）。|

#### 限制输出长度 ####

`git log`有许多非常实用的限制输出长度的选项。`-<n>`表示仅显示最近的 n 条提交，n 可以是任何整数。实践中不太用这个选项，Git 在输出所有提交时会自动调用分页程序，所以你一次只会看到一页的内容。

另外还有按照时间作限制的选项，比如`--since`和`--until`。下面的命令列出所有最近两周内的提交

    $ git log --since=2.weeks

这个命令可以在多种格式下工作，比如说具体的某一天`2008-01-15`，或者是相对地多久以前“2 years 1 day 3 minutes ago”。

还可以给出若干搜索条件，列出符合的提交。用`--author`选项显示指定作者的提交，用`--grep`选项搜索提交说明中的关键字。（如果要得到同时满足这两个选项搜索条件的提交，就必须用 `--all-match`选项。否则，满足任意一个条件的提交都会被匹配出来）

另一个非常有用的筛选选项是`-S`，可以列出那些添加或移除了某些字符串的提交。比如说，你想找出添加或移除了某一个特定函数的引用的提交，你可以这样使用：

    $ git log -Sfunction_name

最后一个很实用的`git log`选项是路径（path），如果只关心某些文件或者目录的历史提交，可以在`git log`选项的最后指定它们的路径。因为是放在最后位置上的选项，所以用两个短划线（--）隔开之前的选项和后面限定的路径名。

以下表格是限制`git log`输出的选项。

|选项|说明|
|:--|:--|
|`-(n)`|仅显示最近的 n 条提交|
|`--since`, `--after`|仅显示指定时间之后的提交。|
|`--until`, `--before`|仅显示指定时间之前的提交。|
|`--author`|仅显示指定作者相关的提交。|
|`--committer`|仅显示指定提交者相关的提交。|
|`--grep`|仅显示含指定关键字的提交|
|`-S`|仅显示添加或移除了某个关键字的提交|

来看一个实际的例子，如果要查看 Git 仓库中，2008 年 10 月期间，Junio Hamano 提交的但未合并的测试文件，可以用下面的查询命令：

    $ git log --pretty="%h - %s" --author=gitster --since="2008-10-01" \
       --before="2008-11-01" --no-merges -- t/
    5610e3b - Fix testcase failure when extended attributes are in use
    acd3b9e - Enhance hold_lock_file_for_{update,append}() API
    f563754 - demonstrate breakage of detached checkout with symbolic link HEAD
    d1a43f2 - reset --hard/read-tree --reset -u: remove unmerged new paths
    51a94af - Fix "checkout --track -b newbranch" on detached HEAD
    b0ad11e - pull: allow "git pull origin $something:$current_branch" into an unborn branch

在近 40000 条提交中，上面的输出仅列出了符合条件的 6 条记录。

### 撤消操作 ###

#### 覆盖上一次提交 ####

有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。此时，可以运行带有`--amend`选项的提交命令尝试重新提交：

    $ git commit --amend

这个命令会将暂存区的文件提交。如果自上次提交以来你还未做任何修改（例如，在上次提交后马上执行些命令），那么快照会保持不变，而你所修改的只是提交信息。新的提交信息会覆盖旧的提交信息。

例如，提交后发现忘记了暂存某些需要的修改，可以像下面这样操作：

    $ git commit -m 'initial commit'
    $ git add forgotten_file
    $ git commit --amend

最终你只会有一个提交，第二次提交将代替第一次提交的结果。

#### 取消暂存的文件 ####

使用`git reset HEAD <file>...`可以取消暂存指定的文件。

#### 撤消对文件的修改 ####

如果想将已经被修改的文件恢复到上次提交成功的状态，可以使用`git checkout -- <file>...`命令撤消之前所做的修改。

记住，在 Git 中任何已提交的东西几乎总是可以恢复的。甚至那些被删除的分支中的提交或使用`--amend`选项覆盖的提交也可以恢复。然而，任何你未提交的东西丢失后很可能再也找不到了。

### 远程仓库的使用 ###

#### 查看远程仓库 ####

如果想查看已经配置的远程仓库服务器，可以运行`git remote`命令，它会列出你指定的每一个远程服务器的简写。如果你已经克隆了自己的仓库，那么至少可以看到 origin --这是Git 给你克隆的仓库服务器的默认名字。

也可以指定选项`-v`，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL。