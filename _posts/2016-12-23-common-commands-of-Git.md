---
layout: post
title:  "Git 常用命令"
categories: Git
tags:  命令
---

* content
{:toc}

Git 是目前世界上最先进的分布式版本控制系统，在学习 Git 的过程中，参照了[廖雪峰](http://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)的 Git 教程和其它网上资料,整理了常用的 Git 命令。





## 术语

- 初始化 Git 仓库：把目录变成 Git 可管理的仓库
- 工作区：电脑里能看到的目录，不包括隐藏目录 .git
- 版本库：既隐藏目录 .git，当前仓库下，如果没有任何的提交，那么版本库就是对应上次提交后的内容
- 暂存区：在版本库中标记为 "index" 或 "stage"，是一个介于工作区和版本库的中间状态
- 分支：每次提交，Git 都把它们串成一条时间线，这条时间线就是一个分支
- HEAD：实际是指向当前分支的一个“游标”

## Git 常用命令

### 配置 Git 仓库

```
$ git init                                             # 在当前目录初始化 Git 仓库
$ git config --global user.name "Your Name"            # 配置用户名
$ git config --global user.email "email@example.com"   # 配置用户邮箱
```

### 文件添加和提交到版本库
```
$ git add [file]           # 把工作区的文件修改添加到暂存区
$ git add -A               # 表示把当前目录中所有 tracked 文件中被修改过或已删除文件和所有 untracted 的文件信息添加到索引库
$ git add -u               # 把当前目录中所有 tracked 文件中被修改过或已删除文件的信息添加到索引库。它不会处理 untracted 的文件
$ git commit -m "..."      # 把暂存区的修改提交到版本库
$ git status               # 查看仓库状态
$ git log --pretty=oneline # 查看提交日志
$ git reflog               # 查看命令记录
```

### 查看差异
```
$ git diff                        # 查看工作区和暂存区的差异
$ git diff -- [file]              # 查看文件在工作区和暂存区的差异
$ git diff --cached               # 查看暂存区和版本库的差异
$ git diff --cached [file]        # 查看文件在暂存区和版本库的差异
$ git diff HEAD                   # 查看工作区和版本库的差异
$ git diff HEAD -- [file]         # 查看文件在工作区和版本库的差异
```

### 撤销修改
```
$ git checkout -- [file]          # 撤销工作区的修改，让文件回到最近一次 git add 时的状态
$ git reset HEAD [file]           # 撤销暂存区的修改，让文件回到最近一次 git commit 时的状态
```

### 删除文件
```
$ git rm [file]                   # 从工作区删除文件，并将这次删除添加到暂存区
$ git rm --cached [file]          # 停止追踪指定文件，但在工作区中保留该文件
```

### 版本回退
```
$ git reset --hard HEAD^          # 回退到往上一个版本
$ git reset --hard HEAD~100       # 回退到往上100个版本
$ git reset --hard 6523476        # 回退到指定版本，6523476是对应的 commit id
```

### 远程仓库

**准备：**关联本地 Git 仓库和 远程 Github 仓库

1. 创建 SSH Key
```
$ ssh-keygen -t rsa -C "youremail@example.com"
#如果一切顺利的话，可以在用户主目录里找到 .ssh 目录，里面有 id_rsa 和 id_rsa.pub 两个文件，这两个就是 SSH Key 的秘钥对，id_rsa 是私钥，不能泄露出去，id_rsa.pub 是公钥，可以放心地告诉任何人。
```

2. 登陆 GitHub，打开 “Account settings”，“SSH Keys” 页面
点 “Add SSH Key”，填上任意 Title，在 Key 文本框里粘贴 id_rsa.pub 文件的内容，点 “Add Key”，你就应该看到已经添加的 Key

```
# 添加远程仓库并命名
$ git remote add [shortname] git@github.com: example/example.git

# 显示所有远程仓库
$ git remote -v

# 显示某个远程仓库的信息
$ git remote show [remote]

# 下载远程仓库的所有变动
$ git fetch [remote]

# 上传本地指定分支到远程仓库
$ git push [remote] [branch]

# 强行推送当前分支到远程仓库，即使有冲突
$ git push [remote] --force

```

### 分支管理
```
$ git branch [shortname]                             # 创建分支
$ git checkout [branch]                              # 切换分支
$ git checkout -b [branch]                           # 创建并切换到该分支
$ git branch --set-upstream [branch]                 # 建立本地分支与远程分支的关联
$ git pull                                           # 抓取远程到本地
$ git merge [branch]                                 # 合并指定分支到当前分支,Fast forward 模式合并，没有 commit，删除分支后，会丢掉分支信息
$ git merge --no-ff -m "merge with no-ff" [branch]   # 普通模式合并，合并后的历史有分支，能看出来曾经做过合并
$ git log --graph --pretty=oneline --abbrev-commit   # 查看合并图
$ git stash                                          # 储藏工作现场
$ git stash list                                     # 查看工作现场
$ git stash pop                                      # 恢复工作现场同时删除 stash
$ git stash apply stash@{0}                          # 恢复工作现场,但不删除 stash
$ git stash drop stash@{0}                           # 删除 stash
$ git branch                                         # 列出所有本地分支
$ git branch -r                                      # 列出所有远程分支
$ git branch -d [branch]                             # 删除分支
$ git branch -D [branch]                             # 强行删除分支
$ git branch -dr [remote/branch]                     # 删除远程分支
```

### 标签管理
```
$ git tag V1.0 [commit_id]                               # 打标签
$ git tag -a V1.0 -m "version V1.0 released" [commit_id] # 创建带有说明的标签
$ git tag                                                # 查看标签
$ git show V1.0                                          # 查看标签详细信息
$ git tag -d V1.0                                        # 删除本地标签
$ git push [remote] V1.0                                 # 推送标签到远程
$ git push [remote] --tags                               # 推送所有标签到远程
$ git push [remote]:refs/tags/v1.0                       # 删除远程标签
```

### 其它

- 忽略文件
  在 Git 工作区的根目录创建一个.gitignore 文件，可以把要忽略的文件名填进去并提交到 Git，Git 就会自动忽略这些文件，示例如下：
  
```
# Windows:
Thumbs.db
ehthumbs.db
Desktop.ini

# Python:
*.py[cod]
*.so
*.egg
*.egg-info
dist
build

# My configurations:
db.ini
deploy_key_rsa
```
