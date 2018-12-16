# Git Notes From Git Pro
---

## 1.1Version Control

### 本地版本控制系统（RCS）
![](https://i.imgur.com/OhvFnK4.png)

> 在硬盘上保存补丁集（补丁是指文件修订前后的变化）；通过应用所有的补丁，可以重新计算出各个版本的文件内容

### 集中化版本控制系统（CVS、Subversion 以及 Perforce）
![](https://i.imgur.com/LyuYa5d.png)
> 系统含有一个单一的集中管理的服务器，保存所有文件的修订版本，而协同工作的人们都通过客户端连到这台服务器，取出最新的文件或者提交更新

### 分布式版本控制系统（Git、Mercurial、Bazaar 以及 Darcs）
![](https://i.imgur.com/9jSrsJ0.png)
> 客户端并不只提取最新版本的文件快照，而是把代码仓库完整地镜像下来。 这么一来，任何一处协同工作用的服务器发生故障，事后都可以用任何一个镜像出来的本地仓库恢复。 因为每一次的克隆操作，实际上都是一次
对代码仓库的完整备份

---

### 1.2Git的一些特点

> **直接记录快照，而非差异比较。**在 Git 中保存项目状态时，它主要对当时的全部文件制作一个快照并保存这个快照的索引。 为了高效，如果文件没有修改，Git 不再重新存储该文件，而是只保留一个链接指向之前存储的文件。 Git 对待数据更像是一个 快照流
#
![](https://i.imgur.com/dzvMVwP.png)
*其他大部分的vcs保存信息的方式*
#
> Git 重新考虑了以前每一代版本控制系统延续下来的诸多方面。 Git 更像是一个小型的文件系统，提供了许多以此为基础构建的
超强工具，而不只是一个简单的 VCS
#
![](https://i.imgur.com/WkBBIwN.png)
*Git保存数据的方式会考虑每一个版本*

> **近乎所有操作都是本地执行**
#
> **Git 保证完整性。**Git 中所有数据在存储前都计算校验和，然后以校验和来引用。 这意味着不可能在 Git 不知情
时更改任何文件内容或目录内容。 这个功能建构在 Git 底层，是构成 Git 哲学不可或缺的部
分。 若你在传送过程中丢失信息或损坏文件，Git 就能发现

### 1.3三种状态、三个区域

##### 三种状态：已提交（committed）、已修改（modified）和已暂存（staged）
- 已提交表示数据已经安全的保存在本地数据库中
- 已修改表示修改了文件，但还没保存到数据库中
- 已暂存表示对一个已修改文件的当前版本做了标记，使之包含在下次提交的快照中
##### 三个区域：Git 仓库、工作目录以及暂存区域
![](https://i.imgur.com/H6tNsJv.png)

- Git 仓库目录是 Git 用来保存项目的元数据和对象数据库的地方。 这是 Git 中最重要的部分，从其它计算机克隆仓库时，拷贝的就是这里的数据。工作目录是对项目的某个版本独立提取出来的内容。 这些从 Git 仓库的压缩数据库中提取出来的文件，放在磁盘上供你使用或修改。
- 暂存区域是一个文件，保存了下次将提交的文件列表信息，一般在 Git 仓库目录中。 有时候
- 也被称作``索引''，不过一般说法还是叫暂存区域。

### 1.4Wokrflow

1. 在工作目录中修改文件。
2. 暂存文件，将文件的快照放入暂存区域。
3. 提交更新，找到暂存区域的文件，将快照永久性存储到 Git 仓库目录。

### 2.1配置

###### Git 自带一个 git config 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在三个不同的位置：

1. /etc/gitconfig 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果使用带有 --
system 选项的 git config 时，它会从此文件读写配置变量。
2. ~/.gitconfig 或 ~/.config/git/config 文件：只针对当前用户。 可以传递 --global
选项让 Git 读写此文件。
3. 当前使用仓库的 Git 目录中的 config 文件（就是 .git/config ）：针对该仓库。每一个级别覆盖上一级别的配置，所以 .git/config 的配置变量会覆盖 /etc/gitconfig 中的配置变量。在 Windows 系统中，Git 会查找 $HOME 目录下（一般情况下是 C:\Users\$USER ）的.gitconfig 文件。 Git 同样也会寻找 /etc/gitconfig 文件，但只限于 MSys 的根目录下，即安装 Git 时所选的目标位置。

###### 配置用户信息
	$ git config --global user.name "John Doe"
	$ git config --global user.email johndoe@example.com

### 2.2 命令行

- git config 配置
- git help 帮助
- git init 初始化一个git仓库
- git add 追踪文件或暂存文件，文件成为暂存状态
- git commit 提交更新，文件成为已提交状态
- git commit -a 跳过使用暂存区域，Git 会自动把所有已经跟踪过的文件暂存起来一并提交，从而跳过 git add 步骤
- git clone 克隆仓库
- git status 查看文件处于什么状态
- git diff 通过文件补丁的格式显示具体哪些行发生了改变,此命令比较的是工作目录中当前文件和暂存区域快照之间的差异,若要查看已暂存的将要添加到下次提交里的内容，可以用 git diff --cached 或 git diff --staged
- git rm 移除文件
- git mv 移动文件（重命名）
- git log 查看提交历史，一个常用的选项是 -p ，用来显示每次提交的内容差异。 也可以加上 -2 来仅显示最近两
次提交，如果想看到每次提交的简略的统计信息，可以使用 --stat 选项，另外一个常用的选项是 --pretty=[oneline|short|full|fuller|format] 。 这个选项可以指定使用不同于默认格式的方式展示提交历史

#### Git下文件的生命周期：
![](https://i.imgur.com/FlHgaBJ.png)

##### git status -s
	git status -s$ git status -s
	M README
	MM Rakefile
	A lib/git.rb
	M lib/simplegit.rb
	?? LICENSE.txt

> 未跟踪文件前面有 ?? 标记，新添加到暂存区中的文件前面有 A 标记，修改过的文件前面有 M 标记(出现在右边的 M 表示该文件被修改了但是还没放入暂存区，出现在靠左边的 M 表示该文件被修改了并放入了暂存区)

##### gitignore
- 所有空行或者以 ＃ 开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式匹配(所谓的 glob 模式是指 shell 所使用的简化了的正则表达式)。
- 匹配模式可以以（ / ）开头防止递归。
- 匹配模式可以以（ / ）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上惊叹号（ ! ）取反
#
	# no .a files
	*.a
	# but do track lib.a, even though you're ignoring .a files above
	!lib.a
	# only ignore the TODO file in the current directory, not subdir/TODO
	/TODO
	# ignore all files in the build/ directory
	build/
	# ignore doc/notes.txt, but not doc/server/arch.txt
	doc/*.txt
	# ignore all .pdf files in the doc/ directory
	doc/**/*.pdf
**[GitHub 有一个十分详细的针对数十种项目及语言的 .gitignore 文件列表](https://github.com/github/gitignore)**

##### git rm

> 要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除（确切地说，是从暂存区域移除），然后提交。 如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f （译注：即 force 的首字母）。 这是一种安全特性，用于防止误删还没有添加到快照的数据，这样的数据不能被 Git 恢复
#
> 另外一种情况是，我们想把文件从Git 仓库中删除（亦即从暂存区域移除），但仍然希望保留在当前工作目录中,则可以使用 --cached 选项

##### git log --pretty=format 常用的选项
![](https://i.imgur.com/yXFsVBm.png)

##### git log 的常用选项
![](https://i.imgur.com/TlnTcwF.png)
##### 限制 git log 输出的选项
![](https://i.imgur.com/DjdPh5y.png)

##### 撤销操作

-  git commit --amend
> 这个命令会将暂存区中的文件提交。 如果自上次提交以来你还未做任何修改（例如，在上次提交后马上执行了此命令），那么快照会保持不变，而你所修改的只是提交信息

- git reset HEAD \<file>

> 撤销对文件的暂存操作

- git checkout — [file]

> 撤销对文件的修改

#### *在 Git 中任何 已提交的 东西几乎总是可以恢复的。 甚至那些被删除的分支中的提交或使用 --amend 选项覆盖的提交也可以恢复。 然而，任何你未提交的东西丢失后很可能再也找不到了*

### 2.3远程仓库

- git remote 查看已经配置的远程仓库服务器,指定选项 -v ，会显示需要读写远程仓库使用的 Git 保存的简写与其对应的 URL
- git remote add \<shortname> \<url> 添加远程仓库
- git fetch [remote-name] 从远程仓库中抓取与拉取，执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看
- git fetch origin 会抓取克隆（或上一次抓取）后新推送的所有工作
- git pull 取回远程主机某个分支的更新，再与本地的指定分支合并
- git push [remote-name] [branch-name] 推送到远程仓库
- git remote show [remote-name] 查看一个远程仓库
- git remote rename [remote-name] [new-name] 重命名远程仓库
- git remote rm [remote-name] 移除远程仓库

### 2.4Tag

##### 轻量标签（lightweight）与附注标签（annotated）

- 轻量标签很像一个不会改变的分支 - 它只是一个特定提交的引用
- 附注标签是存储在 Git 数据库中的一个完整对象。 它们是可以被校验的；其中包含打标签者的名字、电子邮件地址、日期时间；还有一个标签信息；并且可以使用 GNU PrivacyGuard （GPG）签名与验证。 通常建议创建附注标签，这样你可以拥有以上所有信息

##### 命令
- git tag 列出标签
- git tag -a [TAG] -m  [info] 添加附注标签
- git show [TAG] 查看标签信息与对应的提交信息
- git tag [TAG] 添加轻量标签
- git tag [TAG] [提交的校验和（或部分校验和）]

##### 默认情况下， git push 命令并不会传送标签到远程仓库服务器上。 在创建完标签后你必须显式地推送标签到共享服务器上。 这个过程就像共享远程分支一样 - 你可以运行 git push origin [tagname] 。如果想要一次性推送很多标签，也可以使用带有 --tags 选项的 git push 命令。 这将会把所有不在远程仓库服务器上的标签全部传送到那里

##### 为Git命令设置别名，如以下'co' 可代替 'git checkout'
    $ git config --global alias.co checkout
    $ git config --global alias.br branch
    $ git config --global alias.ci commit
    $ git config --global alias.st status

### 3.1Git分支