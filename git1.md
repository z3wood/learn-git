# Git入门系列(1) - 快速了解Git

## 关于版本控制

1. 本地版本控制系统：复制整个项目目录的方式来保存不同的版本
2. 集中化的版本控制系统：有一个单一的集中管理的服务器，保存所有文件的修订版本。如SVN
3. 分布式版本控制系统：客户端并不只提取最新版本的文件快照， 而是把代码仓库完整地镜像下来，包括完整的历史记录。如Git

## 基本的 Git 工作流程

Git 项目拥有三个阶段：工作区、暂存区以及 Git 仓库

1. 在工作区中修改文件。
2. 将你想要下次提交的更改选择性地暂存，这样只会将更改的部分添加到暂存区。
3. 提交更新，找到暂存区的文件，将快照永久性存储到 Git 仓库。

## 安装 Git

### Debian/Ubuntu

```bash
sudo apt-get install git
```

### Windows

官网下载安装包，根据指示进行安装即可

## 运行Git前的配置

### 配置文件

Git 自带一个 `git config` 的工具来帮助设置控制 Git 外观和行为的配置变量。 这些变量存储在三个不同的位置：

1. `/etc/gitconfig` 文件: 包含系统上每一个用户及他们仓库的通用配置。 如果在执行 `git config` 时带上 `--system` 选项，那么它就会读写该文件中的配置变量。 （由于它是系统配置文件，因此你需要管理员或超级用户权限来修改它。）
2. `~/.gitconfig` 或 `~/.config/git/config` 文件：只针对当前用户。 你可以传递 `--global` 选项让 Git 读写此文件，这会对你系统上 **所有** 的仓库生效。
3. 当前使用仓库的 Git 目录中的 `config` 文件（即 `.git/config`）：针对该仓库。 你可以传递 `--local` 选项让 Git 强制读写此文件，虽然默认情况下用的就是它。。 （当然，你需要进入某个 Git 仓库中才能让该选项生效。）

每一个级别会覆盖上一级别的配置，所以 `.git/config` 的配置变量会覆盖 `/etc/gitconfig` 中的配置变量。

### 用户信息

```bash
# 全局设置你的用户名和邮件地址
git config --global user.name "John Doe"
git config --global user.email johndoe@example.com
# 当你想针对特定项目使用不同的用户名称与邮件地址时，可以在那个项目目录下运行没有 --global 选项的命令来配置。
```

### 检查配置信息

```bash
# 列出所有 Git 当时能找到的配置
git config --list
# 检查 Git 的某一项配置
git config <key> 
```

### 获取帮助

```bash
# 三种等价的方法可以找到 Git 命令的综合手册（manpage）
git help <verb>

git <verb> --help 

man git-<verb>
```

## Git 基础

### 获取 Git 仓库

通常有两种获取 Git 项目仓库的方式：

1. 将尚未进行版本控制的本地目录转换为 Git 仓库；
2. 从其它服务器 **克隆** 一个已存在的 Git 仓库。

#### 将尚未进行版本控制的本地目录转换为 Git 仓库

```bash
# 在已存在目录中初始化仓库 (在这个时候，我们仅仅是做了一个初始化的操作，你的项目里的文件还没有被跟踪)
git init

# git add 命令来指定所需的文件加入暂存区
git add LICENSE

# 添加所有未被添加到暂存区的文件到暂存区
git add .

# git commit -m '提交信息' 执行提交，保存一个版本到git仓库
git commit -m 'initial project version'
```

#### 克隆现有的仓库

```bash
# git clone <url> 克隆仓库 (内部已经实现git remote add origin 远程仓库地址)
git clone https://github.com/libgit2/libgit2

# 自定义本地仓库的名字，你可以通过额外的参数指定新的目录名
git clone https://github.com/libgit2/libgit2 mylibgit
```

Git 支持多种数据传输协议。 上面的例子使用的是 `https://` 协议，不过你也可以使用 `git://` 协议或者使用 SSH 传输协议，比如 `user@server:path/to/repo.git` 。

### 文件的状态变化周期

![lifecycle](\imgs\lifecycle.png)

```bash
# 检查当前文件状态
git status

# 跟踪新文件
# 运行了 git add 之后又作了修订的文件，需要重新运行 git add 把最新版本重新暂存起来
git add README

# 提交并保存版本
git commit -m 'commit message'
```

### 忽略文件

我们可以创建一个名为 `.gitignore` 的文件，列出要忽略的文件的模式。

```bash
$ cat .gitignore
*.[oa]
*~
# 第一行告诉 Git 忽略所有以 .o 或 .a 结尾的文件。一般这类对象文件和存档文件都是编译过程中出现的。 
# 第二行告诉 Git 忽略所有名字以波浪符（~）结尾的文件，许多文本编辑软件（比如 Emacs）都用这样的文件名保存副本。
# 要养成一开始就为你的新仓库设置好 .gitignore 文件的习惯，以免将来误提交这类无用的文件
```

文件 `.gitignore` 的格式规范如下：

- 所有空行或者以 `#` 开头的行都会被 Git 忽略。
- 可以使用标准的 glob 模式匹配，它会递归地应用在整个工作区中。
- 匹配模式可以以（`/`）开头防止递归。
- 匹配模式可以以（`/`）结尾指定目录。
- 要忽略指定模式以外的文件或目录，可以在模式前加上叹号（`!`）取反

所谓的 glob 模式是指 shell 所使用的简化了的正则表达式。 星号（`*`）匹配零个或多个任意字符；`[abc]` 匹配任何一个列在方括号中的字符 （这个例子要么匹配一个 a，要么匹配一个 b，要么匹配一个 c）； 问号（`?`）只匹配一个任意字符；如果在方括号中使用短划线分隔两个字符， 表示所有在这两个字符范围内的都可以匹配（比如 `[0-9]` 表示匹配所有 0 到 9 的数字）。 使用两个星号（`**`）表示匹配任意中间目录，比如 `a/**/z` 可以匹配 `a/z` 、 `a/b/z` 或 `a/b/c/z` 等。

我们再看一个 `.gitignore` 文件的例子：

```bash
# 忽略所有的 .a 文件
*.a

# 但跟踪所有的 lib.a，即便你在前面忽略了 .a 文件
!lib.a

# 只忽略当前目录下的 TODO 文件，而不忽略 subdir/TODO
/TODO

# 忽略任何目录下名为 build 的文件夹
build/

# 忽略 doc/notes.txt，但不忽略 doc/server/arch.txt
doc/*.txt

# 忽略 doc/ 目录及其所有子目录下的 .pdf 文件
doc/**/*.pdf
```

GitHub 有一个十分详细的针对数十种项目及语言的 `.gitignore` 文件列表， 你可以在 https://github.com/github/gitignore 找到它。

### 查看已暂存和未暂存的修改

```bash
# 查看修改之后还没有暂存起来的变化内容
git diff

# 比对已暂存文件与最后一次提交的文件差异 --staged和--cached是同义词
git diff --staged
git diff --cached
```

### 从暂存区移除文件

```bash
git rm --cached README
```

### 重命名文件

```bash
# git mv file_from file_to
git mv README.md README
```

### 查看提交历史

```bash
# 不传入任何参数的默认情况下，git log 会按时间先后顺序列出所有的提交，最近的更新排在最上面
git log

# 其中一个比较有用的选项是 -p 或 --patch ，它会显示每次提交所引入的差异（按 补丁 的格式输出）。 你也可以限制显示的日志条目数量，例如使用 -2 选项来只显示最近的两次提交
git log -p -2

# 图形展示
git log --graph
```

### 撤消操作

```bash
# 有时候我们提交完了才发现漏掉了几个文件没有添加，或者提交信息写错了。 此时，可以运行带有 --amend 选项的提交命令来重新提交，最终你只会有一个提交——第二次提交将代替第一次提交的结果。
git commit --amend

# 取消暂存的文件 git reset HEAD <file>... 
git reset HEAD CONTRIBUTING.md

# 撤消对文件的修改
git checkout -- CONTRIBUTING.md

# 回滚旧版本
git log
git reset --hard 版本号

# 回滚未来版本
git reflog
git reset --hard 版本号
```



## Git分支

```bash
# 查看所有分支以及当前所在分支
git branch

# 分支创建 默认从当前所处分支状态创建
git branch testing

# 分支切换
git checkout testing

# 创建分支并切换 起点分支不写则默认从当前所处分支状态创建
git checkout -b 分支名 [起点分支]

# 合并分支 首先要回到你的 master 分支 此时会把hotfix分支内容合并到master
#  如果顺着一个分支走下去能够到达另一个分支，那么 Git 在合并两者的时候， 只会简单的将指针向前推进（指针右移），因为这种情况下的合并操作没有需要解决的分歧——这就叫做 “快进（fast-forward）”
# Git 会在有冲突的文件中加入标准的冲突解决标记，这样你可以打开这些包含冲突的文件然后手动解决冲突，之后再合并
git checkout master
git merge hotfix

# 删除分支
git branch -d hotfix

# 查看哪些分支已经合并到当前分支
git branch --merged

# 查看哪些分支未合并到当前分支
git branch --no-merged

# 变基 将experiment变为指向master  此时再回到master就可以进行一次快进合并 如果有冲突需要自己解决冲突再rebase
git checkout experiment
git rebase master

git checkout master
git merge experiment
```

![工作流](\imgs\git2.jpg)

## 远程仓库的使用

### 常用命令

```bash
# 查看你已经配置的远程仓库服务器
git remote

# 添加远程仓库
git remote add <shortname> <url>

# 从远程仓库中抓取与拉取
## 这个命令会访问远程仓库，从中拉取所有你还没有的数据。 执行完成后，你将会拥有那个远程仓库中所有分支的引用，可以随时合并或查看。
git fetch <remote> <branch>
## 自动抓取后合并该远程分支到当前分支, git pull 根据配置的不同，可为git fetch + git merge 或 git fetch + git rebase，一般不建议使用git pull
git pull <remote> <branch>

# 推送到远程仓库 git push <remote> <branch>
git push origin master

# 远程仓库的重命名与移除
## 重命名
git remote rename old_name new_name
## 移除
git remote remove <remote>

# 删除远程分支 git push origin --delete 分支名
git push origin --delete serverfix
```

![常用命令示意图](\imgs\git1.jpg)

### 免密码登录

#### URL中体现

```bash
# 原来的地址：https://github.com/xxx/xxx.git
# 修改的地址：https://用户名:密码@github.com/xxx/xxx.git
git remote add origin https://用户名:密码@github.com/xxx/xxx.git
git push origin master
```

#### SSH实现

```bash
# 生成公钥和私钥(默认放在 ~/.ssh目录下 id_rsa.pub公钥 id_rsa私钥
ssh-keygen

# 拷贝公钥并设置到github

# 在git本地配置ssh地址
git remote add origin git@github.com:xxx/xxx.git

# 以后使用
git push origin master
```

#### git自动管理凭证