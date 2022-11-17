# Git

集中式版本控制工具：版本库集中存放于中央服务器

分布式版本控制工具：每个参与项目的人电脑上都有一个完整的版本库，工作时无需联网，多人协作只需要将各自的修改推送给对方



## 工作流程

```
远程仓库（抓取/克隆、推送）本地仓库
```

- clone：从远程仓库中克隆代码到本地
- checkout：检出，从本地仓库中检出一个仓库分支然后修改、提交
- add：添加，在提交之前将代码提交到暂存区
- commit：提交，提交到本地仓库，本地仓库中保存修改的各个历史版本
- fetch：抓取，从远程库抓取到本地仓库
- pull：拉取，从远程库拉到本地仓库，自动合并，相当于 fetch + merge
- push：推送，修改完成后，将代码推送到远程仓库

## 基本配置

设置用户信息：

```
git config --global user.name "xxx"

git config --global user.email "xxxx"
```

查看用户信息：

```
git config --global user.name

git config --global user.email
```

## 解决中文乱码问题

在 GitBash 中执行命令：

```
git config --global core.quotepath false
```

在 Git 安装目录下的 `/etc/bash.bashrc` 文件最后加入两行：

```
export LANG="zh_CN.UTF-8"
export LC_ALL="zh_CN.UTF-8"
```

## 常用指令

Git 工作目录的几种状态：

![image-20221114133544106](Git.assets/image-20221114133544106.png)

- 暂存区：提交到仓库之前的缓存区（git add .）
- 将暂存区内容提交到仓库后，每次提交生成提交记录（生成历史版本，此时代码删不掉）

常用命令：

- `git config --global user.name` 用户名 （设置用户签名）
- `git config --global user.email` 邮箱（这两个命令只需首次使用时候设置）
- `git init`初始化本地仓库
- `git status`查看本地库状态
- `git add FILENAME`，`git add .`添加到暂存区
- `git commit -m "MESSAGE" FILENAME`，`git commit -m "MESSAGE"`提交到本地库
- `git reflog` 查看历史版本
- `git reset --hard VERSION_CODE` 版本穿梭

### 设置用户签名

`git config --global user.name` 和`git config --global user.email`设置用户签名，用于区别代码提交者，用户的签名信息可以在每一个版本的提交信息中看到，从而确定提交由谁发起，Git 首次使用必须设置一下用户签名，否则无法提交代码，此外，注意此处设置的用户签名与登录 Github 或其他代码托管中心的账号没有任何关系

设置完成后可在用户的系统目录下，`.gitconfig`文件中看到

### 初始化本地库

使用`git init`初始化仓库

### 新增文件

使用命令 `git add FILENAME`将工作区文件提交到暂存区

使用命令`git rm --cached FILENAME` 移除提交到暂存区的文件（工作区为改变）

这一步易出现的警告：

```
warning: LF will be replaced by CRLF in filexxx
```

这是因为在安装 Git 的时候勾选了自动根据系统切换换行符（windows 下换行符是 CRLF，Linux 下则是 LF）

### 提交到本地仓库

使用命令`git commit -m "MESSAGE"FILENAME`，将暂存区内容提交到本地仓库

### 查看提交日志

使用命令`git log`查看提交日志，可选参数有：

- --all  显示所有分支
- --pretty=oneline  将提交信息显示为一行
- --abbrev-commit  简化输出信息
- --graph  以图的形式显示

为了使用方便，可以使用别名设置简化命令，在`bash.bashrc`中添加：

```
alias git-log='git log --pretty=oneline --all --graph --abbrev-commit'
# alias 用于设置别名 例如：
alias ll='ls -al'

source ~/bash
```

**查看所有版本（即使回退也可以看到）**：使用 `git reflog`命令

### 版本回退

版本回退用于版本切换：

```
git reset --hard commitID
```

commitID 可以使用 `git-log`或者`git reflog`命令查看

## 分支

版本控制的过程中，为了同时推进多个任务，可以为每个任务创建单独的分支，将每个任务从研发主线上分离出来，这样开发自己分支的同时不会影响主线运行（分支的底层通过指针引用实现）

分支的主要命令：

- `git branch NAME`创建分支
- `git branch -v`查看分支
- `git checkout NAME`切换分支
- `git merge NAME`将指定分支合并到当前分支上
- `git branch -d NAME`，先移动到其他分支，再通过此命令删除分支，如果删除不成功，则使用强制删除`git branch -D NAME`

### 合并冲突

不同分支对于同一文件同一位置进行修改

## 团队协作

团队合作开发有两种方式：

- 团队内协作
  - 通过 push - clone - pull 操作完成
  - 每个成员都可以将自己的修改 push 到远程仓库
  - 团队成员可以通过 clone 将远程仓库克隆到本地
  - 代码修改后，团队成员可以通过 pull 将远程仓库拉取到本地
- 跨团队协作
  - 团队之外的人可以通过 fork 仓库对仓库内容修改（可提交多次）
  - 修改完成后进行 pull request，将修改交由仓库管理员审核

### 拉取、推送代码

- `git remote -v`显示所有的远程库
- `git remote add REPOSITORY_NAME ADDRESS`：添加远程仓库，并设置别名（REPOSITORY_NAME）
- `git clone ADDRESS`：克隆到本地，克隆会拉取代码，并进行本地初始化以及创建别名（origin）
- `git pull REPOSITORY_NAME BRANCH_NAME `

## 通过 ignore 屏蔽不想提交的文件

语法规范：

- `#`：注释

- `*`：用于匹配零个或多个字符：

  ```git
  *.a  -- 忽略 .a 文件
  *.[oa] -- 忽略所有的 .a 和 .o 文件 [] 表示匹配括号内的任意字符，例如[abc]表示匹配a，b，c三个字符
  ```

- `!` ：否定忽略 例如 !a.txt 表示 a.txt 不会被忽略

- `/`：匹配目录

  - `/bin` 表示忽略根目录下的 bin 文件夹，但是不会忽略 bin 文件夹下的子文件夹，例如`/bin/file`
  - `bin/`表示忽略 bin 文件夹下的所有子内容，但是不会忽略 bin 文件夹本身

- `**`：匹配多级目录

`.gitignore`文件中每行匹配一个规则，常用的例子如：

```
# 忽略所有的bin文件
*.bin

# 跳过指定文件
!a.bin
!/files/b.bin

# 忽略指定目录下的 txt 文件，但不包括该目录的子目录，例如file1/file2/a.txt
file1/*.txt

# 忽略某个目录下的所有 txt 文件
file1/**/*.txt

# 忽略当前目录的 txt 文件，但不包括子目录的 txt 文件
/*.txt

# 忽略当前文件夹下的 file 文件夹下的所有内容
file/

# 忽略所有目录中的 test 文件夹： /test a/test a/b/test
**/test

# 忽略 node_modules 文件夹
node_modules/
```

