---
layout: post
title: Git命令大全
categories: tools
description: git使用
keywords: tools, git, github
---

Git命令大全

### 1、初始化配置

```bash
#配置使用git仓库的人员姓名

git config --global user .name "Your Name Comes Here"

#配置使用git仓库的人员email

git config --global user . email you@yourdomain. example. com

#配置到缓存默认15分钟

git config --g1obal credential. helper cache

#修改缓 存时间

git config --global credential. helper ' cache - -timeout=3600 '

it config --globa1 color.ui true

git config --globa1 alias.co checkout

git config --global alias.ci commit

git config --global alias.st status

git config --globa1 alias.br branch

git config --global core. editor "mate -w"

#设置Editor使用textmate

git config -1 #列举所有配置

#用户的git配置文件~/ . gitconfig
```

### 2、文件操作

查看、添加、提交、删除、找回，重置修改文件

```bash
#显示command的help
git help < command> 

#显示某次提交的内容
git show  # 显示某次提交的内容git show $id

#抛弃工作区修改
git co -- <fi1e>

#抛弃工作区修改
gitco。

#将工作文件修改提交到本地暂存区
git add <fi1e>

#将所有修改过的工作文件提交暂存区
git add .

#从版本库中删除文件
git rm <fi1e>

#从暂存区恢复到工作文件
git rm <file> --cached #从版本库中删除文件，但不删除文件

# 退回上一个版本
git reset <fi1e>

#从暂存区恢复到工作文件
git reset -- .

#恢复最近一次提交过的状态，即放弃上次提交后的所有本次修改
git reset --hard

#将git add, git rm和git ci等操作都合并在一起做
git ci <file>

git ci .

git ci -a

git ci -am "some comments "

git ci --amend  #修改最后一次提交记录

#恢复某次提交的状态，恢复动作本身也创建了- -次提交对象
git revert <$id>

#恢复最后一次提交的状态
git revert HEAD

```

### 3、查看文件diff

```bash
# 比较当前文件和暂存区文件差异 git diff
git diff <file> 

# 比较两次提交之间的差异
git diff <id1><id1><id2> 

# 在两个分支之间比较
git diff <branch1>..<branch2> 

# 比较暂存区和版本库差异
git diff --staged 

# 比较暂存区和版本库差异
git diff --cached 

# 仅仅比较统计信息
git diff --stat 
```

### 4、查看提交记录

```bash
# 查看该文件每次提交记录
git log git log <file> 

# 查看每次详细修改内容的diff
git log -p <file> 

# 查看最近两次详细修改内容的diff
git log -p -2 

#查看提交统计信息
git log --stat 
```

### 5、取得git库

```bash

#初始化一个版本仓库
git init

#Clone远程版本库
git clone git@xbc . me: wordpress.git

#添加远程版本库 origin，语法为git remote add [ shortname] [ur1]
git remote add origin git@xbc . me :wordpress.git

#查看 远程仓库
git remote -v
```

### 6、提交你的修改

```bash
#添加 当前修改的文件到暂存区
git add

#如果你自动追踪文件，包括你已经手动删除的，状态为Deleted的文件
git add -u

#提交你的修改
git commit -m "你的注释"

#推送你的更新到远程服务器,语法为git push [远程名] [本地分支]:[远程分支]
git push origin master

#查看文件状态
git status

#跟踪新文件
git add readme.txt

#从当前跟踪列表移除文件，并完全删除
git rm readme.txt

#仅在暂存区删除，保留文件在当前目录，不再跟踪
git rm -cached readme.txt

#重命名文件
git mv reademe. txt readme

#查看提交的历史记录
git 1og .

#修改最后- -次提交注释的，利用-amend参数
git commit -- amend

#忘记提交某些修改，下面的三条命令只会得到一 一个提交。24. git commit -m &quot;add readme . txt&quot;
git add readme_ forgotten

git commit -amend

#假设你已经使用git add .， 将修改过的文件a、b加到暂存区28. #现在你只想提交a文件，不想提交b文件，应该这样
git reset HEAD b

#取消对文件的修改
git checkout -- readme.txt
```

### 7、本地分支管理

查看、切换、创建和删除分支

```bash
# 查看远程分支
git br -r 

# 创建新的分支
git br <new_branch> 

# 查看各个分支最后提交信息
git br -v 

# 查看已经被合并到当前分支的分支
git br --merged 

# 查看尚未被合并到当前分支的分支
git br --no-merged 

# 切换到某个分支
git co <branch> 

# 创建新的分支，并且切换过去
git co -b <new_branch> 

# 基于branch创建新的new_branch
git co -b <new_branch> <branch> 

# 把某次历史提交记录checkout出来，但无分支信息，切换到其他分支会自动删除
git co $id 

# 把某次历史提交记录checkout出来，创建成一个分支
git co $id -b <new_branch> 

# 删除某个分支
git br -d <branch> 

# 强制删除某个分支 (未被合并的分支被删除的时候需要强制)
git br -D <branch> 
```

### 8、分支合并和rebase

```bash
#将branch分支合并到当前分支
git merge <branch>

#不要Fast-Foward 合并，这样可以生成merge提交
git merge origin/master --no-ff


#将master rebase到branch
git rebase master <branch>

```

### 9、Git补丁管理

方便在多台机器上开发同步时用

```bash
#生成补丁
git diff > . ./sync. patch

#打补丁
git apply . ./sync. patch

#测试补丁能否成功
git app1y --check . ./sync. patch
```

### 10、Git暂存管理

```bash
#暂存
git stash

#列所有stash
git stash list

#恢复暂存的内容
git stash apply

#删除暂存区
git stash drop
```

### 11、Git远程分支管理

```bash
#抓取远程仓库所有分支更新并合并到本地
git pu1l

#抓取远程仓库所有分支更新并合并到本地，不要快进合并
git pu11 --no-ff

#抓取远程仓库更新
git fetch origin


#将远程主分支合并到本地当前分支
git merge origin/master


#跟踪某个远程分支创建相应的本地分支
git co --track origin/ branch

#基于远程分支创建本地分支，功能同上
git co -b <1ocal_ _branch> origin/<remote_ _branch> 

# push 所有分支
git push


#将本地主分支推到远程主分支
git push origin master


#将本地主分支推到远程(如无远程主分支则创建，用于初始化远程仓库)
git push -u origin master


#创建远程分支，origin是远程仓库名
git push origin <1oca1_ branch>

# 创建远程分支
git push origin <1oca1_ branch>:<remote_ branch> 

#先删除 本地分支(git br -d <branch>)， 然后再push删除远程分支
git push origin :<remote_ branch> 

```

### 12、基本的分支管理

```bash
 #创建 一个分支
git branch dev

#切换工作目录到dev
git chekcout dev

#将上面的命令合在一起，创建dev分支并切换到dev
git chekcout -b dev

#合并dev分支，当前工作目录为master
git merge dev

#合并完成后，没有出现冲突，删除dev分支
git branch -d dev

#拉去远程仓库的数据，语法为git fetch [remote-name]
git fetch

#fetch 会拉去最新的远程仓库数据，但不会自动到当前目录下，要自动合并
git pu1l

#查看远程仓库的信息
git remote show origin

#建立本地的dev分支追踪远程仓库的develop分支
git checkout -b dev origin/ develop
```

### 13、Git远程仓库管理

```bash
#查看远程服务器地址和仓库名称
git remote -V

#查看远程服务器仓库状态
git remote show origin


#添加远程仓库地址
git remote add origin git@ github:robbin/robbin_ site.git

#设置远程仓库地址(用于修改远程仓库地址)
git remote set-url origin git@ github . com: robbin/robbin_ site.git 

#删除远程仓库
git remote rm <repository>
```

### 14、创建远程仓库

```bash
#用带版本的项目创建纯版本仓库
git clone --bare robbin_ site robbin_ site.git 

#将纯仓库上传到服务器上
scp一r my_ project.git git@ git.csdn.net:~

#在服务器创建纯仓库
mkdir robbin_ site.git && cd robbin_ site.git && git --bare init 

#客户端首次提交
git remote add origin git@ github. com: robbin/ robbin_ site.git#设置远程仓库地址5. git push -u origin master

#首次将本地develop分支提交到远程develop分支，并且track
git push -u origin develop 

#设置远程仓库的HEAD指向master分支
git remote set-head origin master

```

也可以命令设置跟踪远程库和本地库

```bash
git branch --set-upstream master origin/ master
git branch --set-upstream develop origin/ develop
```
