- [Git版本控制和连接GitHub](#git-------github)
  * [一、Git简介](#--git--)
    + [1. Git概述](#1-git--)
  * [二、本地库基本操作](#---------)
    + [1. 创建本地仓库repository](#1-------repository)
    + [2. 补充版本库(Repository)知识](#2-------repository---)
    + [3. 管理修改](#3-----)
    + [4. 版本回退](#4-----)
  * [三、远程库基本操作](#---------)
    + [1.添加远程库](#1-----)
    + [2. 克隆远程库到本地](#2---------)
    + [3.创建与合并分支](#3-------)
    + [4.提交代码到远程库](#4--------)
  * [四、添删远程库和username、email等问题](#--------username-email---)
  
# Git版本控制和连接GitHub

## 一、Git简介

### 1. Git概述

> **Git**先进的分布式 <u>版本控制</u> 系统, **GitHub**是Git常用的 <u>代码托管中心</u>       **~~Git = GitHub~~**

[Git官网]: https://git-scm.com/	"win|| linux|| mac"

安装完成后,在任何空白位置上右击 **git bash here** 输入 **git --version**,有版本提示即成功.

```shell
git --verion
```

## 二、本地库基本操作

### 1. 创建本地仓库repository

- 首先,在合适的位置上 **创建空白目录(仓库)**, `git` 命令有很多跟`linux`相通的 例如 `cd `、`ls`等,内置vim

```shell
MECHREVO@□□е□□□□ MINGW64 ~/Desktop
$ mkdir MyGitRepo					# 创建目录

MECHREVO@□□е□□□□ MINGW64 ~/Desktop
$ cd MyGitRepo/

MECHREVO@□□е□□□□ MINGW64 ~/Desktop/MyGitRepo
$
```

- 其次, 通过 **初始化** 仓库,使得该目录可以被Git管理

```shell
MECHREVO@□□е□□□□ MINGW64 ~/Desktop/MyGitRepo
$ git init
Initialized empty Git repository in C:/Users/Administrator/Desktop/MyGitRepo/.git/

MECHREVO@□□е□□□□ MINGW64 ~/Desktop/MyGitRepo (master)
$ ls -la					# 查看当前文件目录情况
total 12
drwxr-xr-x 1 MECHREVO 197121 0  7月 22 12:54 ./
drwxr-xr-x 1 MECHREVO 197121 0  7月 22 12:49 ../
drwxr-xr-x 1 MECHREVO 197121 0  7月 22 12:54 .git/
```

.git是隐藏文件目录,里面存放的是跟踪管理版本库,一般不要随意修改,否则会破坏Git仓库不能用 

- 设置签名, 区分  <u>不同的开发人员身份</u> ,这里设置的签名和登录远程库(代码托管中心)的账号、密码没有任何关系.  签名分 **项目(仓库)级别** 适用当前本地库范围 和 **系统用户级别** 适用当前操作系统的用户都存在的时候,优先项目级别. 至少又一个.

```shell
# 项目级别/仓库级别
$ git config user.name jackmin
$ git config user.email xxxxxx@xxx.com
# 系统用户级别
$ git config --global user.name jackmin
$ git config --global user.email xxxx@xxx.com
```

- 查看git状态  

```sh
MECHREVO@□□е□□□□ MINGW64 ~/Desktop/MyGitRepo (master)
$ git status                                                                     On branch master
No commits yet
nothing to commit (create/copy files and use "git add" to track)
```

### 2. 补充版本库(Repository)知识

- 工作区有一个隐藏目录`.git`，这个不算工作区，而是**Git的版本库**。
- Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支`master`，以及指向`master`的一个指针叫`HEAD`。

![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/Git_1.png)

- 有分支和`HEAD`的概念,我们把文件往Git版本库里添加的时候，是分两步执行的：

​       第一步是用`git add`把文件添加进去，实际上就是把文件修改添加到暂存区；

​       第二步是用`git commit`提交更改，实际上就是把暂存区的所有内容提交到当前分支master。(这里的master不是github上的)

### 3. 管理修改

- 尝试查看当前的status

```shell
MECHREVO@□□е□□□□ MINGW64 ~/Desktop/MyGitRepo (master)
$ git status
On branch master

No commits yet

Untracked files:
  (use "git add <file>..." to include in what will be committed)

                        		text.txt	# 红色

nothing added to commit but untracked files present (use "git add" to track)
```



<font color=red>text.txt</font> 是<u>红色字体</u>,表明当前<u>**文件存在工作区**,但是**需要提交到暂存区 **</u>

- 通过提交到暂存区**`git add <file>`** ,文件名支持**正则匹配**那种 (如果存在多个后缀是.txt文件,用*.txt)

```shell
$ git add *.txt		# 所有以.txt结尾的文本都会被加入到暂存区
warning: LF will be replaced by CRLF in text.txt.
The file will have its original line endings in your working directory
# 再次查看git状态
$ git status                                                                             On branch master

No commits yet

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   text.txt				# 绿色


```

 <font color=green>new file:  text.txt</font>  是 <u>绿色字体</u> 表示 <u>**已经加到了暂存区** , **需要提交到master**</u>

如果要从 <u>**暂存区撤回**</u> , 执行`git rm --cached  <file>`也是可以用正则匹配的

```shell
$ git rm --cached <file>
```

- 执行 git commit text.txt将暂存区文件提交到master,会默认打开vim编辑窗口要求在第一行输入 commit message

```shell
$ git commit text.txt
```

![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/Git_2.png)

通过这种方式必须要输入,否则不予许commit;完毕再次查看当前git状态

```shell
$ git status
On branch master
nothing to commit, working tree clean
```

`git diff HEAD -- readme.txt` 命令可以查看工作区和版本库里面最新版本的区别

- 打开vim输入觉得麻烦,可以直接输入`git commit -m “here are commit message” <file>`直接给定commit message然后提交

```
$ git commit -m "This is the second time to write the message" text.txt
On branch master
nothing to commit, working tree clean
```

- 如果修改了text.txt文件而没有提交,则运行`git status`会提示 <font color =red>modified :	text.txt</font> 红色字体,被修改.需要再次add  ,commit
- 查看git 日志 `git log`

```shell
MECHREVO@□□е□□□□ MINGW64 ~/Desktop/MyGitRepo (master)
$ git log
commit 90f022e03b9137b51d6434caf10058d161b69524 (HEAD -> master)
Author: Jack Chen <1416825008@qq.com>
Date:   Mon Jul 22 15:04:12 2019 +0800

    This is the second time to write the message

commit 353ed8f1bc9e0d69081612645c91532b59ffe142
Author: Jack Chen <1416825008@qq.com>
Date:   Mon Jul 22 14:28:31 2019 +0800

    This is the first time to write the message

```

### 4. 版本回退

可以看到有两次提交日志, 而**head 指针**指向了**最近一次修改**. 推荐使用  <u>基于索引值的版本进退</u>, 

每次修改会有记录,如果仅仅撤销上次commit message并修改可用:

```shell
$ git commit --amend -m "commit message"
```

- 把改变当前工作目录中文件恢复到指定的工作状态,默认最新

```shell
$ git checkout "90f022e" 
```

- 当使用 `git checkout --hard "90f022e"` 撤销了这几次commit时,又想重做这几次的commit

`git reflog` 和 `git reset`

```shell
$ git reflog
$ git reset "90f022e"
```

`git log` 列出的是生效的, 没有丢弃的commit, `git reflog `查找到被`git checkout --hard `“抛弃”掉的那几次commit的sha值通过`git reset`恢复到想要的commit上去。

- commit了几次后发现错误地commit到了master分支上了，你只是想commit到feature分支上。

```shell
git branch feature然后 git reset --hard origin/master 最后git checkout feature
```

- 图片来源网络
![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/Git_3.png)

慎用 `git rebase` 会变基,会产生代码冲突(蠢货给 push了)

当Bob需要从远程pull拉取feature时候,就会出现代码冲突.

<u>rebase就是bob->you的变化;merge还是两个分支，只不过在merge后这个点交汇</u>

<u>rebase 提示错误一个个解决,中途可以退出;merge则是全显示出来</u>

- 图片来源网络

![image](https://github.com/JackMin1314/Learning_Skill/raw/master/WSL_pic/Git_4.png)

<u>`git pull –rebase` 的理解:</u>
这个命令做了以下内容： 
a.把你 commit 到本地仓库的内容，取出来放到暂存区(stash)（这时你的工作区是干净的） 
b.然后从远端拉取代码到本地，由于工作区是干净的，所以不会有冲突 

c.从暂存区把你之前提交的内容取出来，跟拉下来的代码合并 .

## 三、远程库基本操作

### 1.添加远程库

- 登陆`GitHub`，然后，在右上角找到“`Create a new repo`”按钮，创建一个**新的仓库** 或者关联已经存在的仓库.
- 进入仓库目录点击 <font color =green>`clone or download`</font> 选择 clone with https 复制链接  例如：https://github.com/JackMin1314/Learning_Skill.git
- 查看当前库的远程库情况,发现没有任何远程库提示说明

```shell
$ git remote -v
```

- 关联远程仓库

```shell
$ git remote add origin https://github.com/JackMin1314/Learning_Skill.git
```

- 再次查看当前远程库情况,发现已经添加了

```shell
$ git remote -v
origin  https://github.com/JackMin1314/Learning_Skill.git (fetch)
origin  https://github.com/JackMin1314/Learning_Skill.git (push)
```

### 2. 克隆远程库到本地

- 用`git clone  <https://...>` 可以将远程库复制到本地.

```shell
$ git clone https://github.com/JackMin1314/Learning_Skill.git
```

```shell
MECHREVO@□□е□□□□ MINGW64 ~/Desktop/MyGitRepo ((90f022e...))
$ git clone https://github.com/JackMin1314/Learning_Skill.git
Cloning into 'Learning_Skill'...
remote: Enumerating objects: 24, done.
remote: Counting objects: 100% (24/24), done.
remote: Compressing objects: 100% (21/21), done.
remote: Total 24 (delta 1), reused 15 (delta 0), pack-reused 0
Unpacking objects: 100% (24/24), done.
# 再次查看当前目录,发现有Learning_Skill 文件夹,进入后发现github上的文件都在里面
MECHREVO@□□е□□□□ MINGW64 ~/Desktop/MyGitRepo ((90f022e...))
$ ls
Learning_Skill/  text.txt
```

### 3.创建与合并分支

- 查看当前分支和创建 `git branch  <branchname>` 删除 `git branch -d <branchname>` 分支

```shell
$ git branch										# 查看分支和当前分支
* master
$ git branch  myBranch						        # 添加分支myBranch
$ git branch -d myBranch						    # 删除分支
```

创建成功后,再次查看分支

```shell
$ git branch										# 查看分支和当前分支
* master
  myBranch
$ git checkout myBranch								# 切换到指定分支
$ git branch
$ git branch										# 查看分支和当前分支
  master
* myBranch
```

**尚未推送或分享给别人的本地修改**可以执行变基操作来清理历史(commit message)----git rebase

**已推送至别处的提交**不执行变基操作

- `--pretty=oneline`  表示只显示一行哈希值和提交说明

```shell
git log --graph --pretty=oneline --abbrev-commit
```

### 4.提交代码到远程库

尝试提交一下到远程库

```shell
$ git push origin master
```

但是报错!!!

```shell
$ git push origin master
To https://github.com/JackMin1314/Learning_Skill.git
 ! [rejected]        master -> master (non-fast-forward)
error: failed to push some refs to 'https://github.com/JackMin1314/Learning_Skill.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

根据提示需要先git pull....说明本地库不是最新的,尝试git pull origin master,还是失败

```
$ git pull --rebase origin master
From https://github.com/JackMin1314/Learning_Skill
 * branch            master     -> FETCH_HEAD
 * [new branch]      master     -> origin/master
First, rewinding head to replay your work on top of it...
Applying: This is the first time to write the message
Applying: This is the second time to write the message
Applying: Sort out some files
```



## 四、添删远程库和username、email等问题

- 输入  **`git config --h`** 可以查看很多命令,例如查看全局配置文件(系统级别)中的信息

```shell
$ git config --global -l                                                                 user.name=Jack Chen                                                                        user.email=1416825008@qq.com 
```

- 输入 `git config --global -l ` 可以查看所有信息
- 查看文档选择--unset remove a variable: name [value-regex]

```shell
$ git config --global --unset user.email		# 清空 user.email 和 user.username
$ git config --global --unset user.name
```

- 查看信息没有任何显示直接看username 和email按  **`git config --global --get user.name`**

```shell
$ git commit --amend -m head                                                                                            
*** Please tell me who you are.

Run

  git config --global user.email "you@example.com"
  git config --global user.name "Your Name"

to set your account's default identity.
Omit --global to set the identity only in this repository.

fatal: unable to auto-detect email address
```

- <u>重新添加name,email</u>用 `user.email` ,  `user.name`(这里设置全局的)

```shell
$ git config --global user.email "1416825008@qq.com"
$ git config --global user.name "jackmin"
$ git config --global -l
user.email=1416825008@qq.com
user.name=jackmin
```

下面重新开始

```shell
# 克隆最新的远程库
$ git clone https://github.com/JackMin1314/Learning_Skill.git
$ git pull https://github.com/JackMin1314/Learning_Skill.git
# 修改了几次后提交,再push报错
$ git push origin master                                                                 To https://github.com/JackMin1314/Learning_Skill.git
 ! [rejected] master -> master (non-fast-forward)
error: failed to push some refs to 'https://github.com/JackMin1314/Learning_Skill.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
# 尝试用最新的远程库和本地变基
$ git pull --rebase origin master                                                         From https://github.com/JackMin1314/Learning_Skill
 * branch  master   -> FETCH_HEAD
First, rewinding head to replay your work on top of it...
# 查看当前状态没问题
$ git status
# 直接提交到远程
$ git pull origin master                                                                 From https://github.com/JackMin1314/Learning_Skill
 * branch            master     -> FETCH_HEAD
Already up to date.
$ git push origin master
Enumerating objects: 2, done.
Counting objects: 100% (2/2), done.
Delta compression using up to 12 threads
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 447 bytes | 447.00 KiB/s, done.
Total 2 (delta 0), reused 0 (delta 0)
To https://github.com/JackMin1314/Learning_Skill.git
   f25daf1..c699f70  master -> master
```

成功.去github看到修改了对应目录.
