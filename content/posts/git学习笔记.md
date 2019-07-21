---
title: git学习笔记
date: 2019-01-16T13:52:57
toc: false
tags: 
  - 工具
  - github
---
----

### 引言
git是14年才接触的，然后也没有系统的学习过。都是平时遇到一点问题，去搜一搜。

安装部分就不说了。

### 概念

![](https://blog-pics.pek3b.qingstor.com/006tNc79ly1fz9dildkoxj30cq06iaa8.jpg)

1. 工作区
就是电脑上写代码部分
2. 版本库
Git的版本库里存了很多东西，其中最重要的就是称为stage（或者叫index）的暂存区，还有Git为我们自动创建的第一个分支master，以及指向master的一个指针叫HEAD。

### 常用命令

#### 初始化

>
mkdir learngit            新建一个文件夹<br>
cd learngit               并进入该文件夹<br>
git init                  初始化，把该文件夹变成git可以管理的仓库（会产生一个.git文件）<br>
git add <file>            把文件修改添加到暂存区（Stage）<br>
git commit -m <message>   把暂存区的所有内容提交到当前分支<br>

#### 查看状态及回退
> 
git status                  查看当前仓库状态<br> 
git diff                    查看详细的修改内容<br>
git diff HEAD -- readme.txt 查看工作区和版本库里面最新版本的区别<br>
git log                     查看从最近到最远的提交日志<br>
git log --pretty=oneline    查看从最近到最远的简洁版提交日志<br>
git log --graph             查看分支合并图<br>
git reflog                  查看历史命令<br>
git reset --hard HEAD^      回退到上一个版本<br>
git reset --hard HEAD^^     回退到上上一个版本<br>
git reset --hard HEAD~10    回退到往上10个版本<br>
git reset --hard commit_id  回退到固定某个版本<br>

#### 放弃修改
> git checkout -- <file>      丢弃工作区的修改<br>
  git reset HEAD <file>       丢弃暂存区的修改，回到工作区<br>
  git rm <file>               从版本库中删除掉某文件<br>

#### 关联
> git remote add origin git@server-name:path/repo-name.git  关联一个远程仓库<br>
  git push -u origin <name>   关联后，第一次推送master分支的所有内容<br>
  git push origin <name>      以后的正常提交<br>
  git checkout -b <name>      新建dev分支并切换到dev分支<br>
  相当于
  git branch <name>           新建dev分支<br>
  git checkout <name>         切换到dev分支<br>

#### 合并删除分支
> git branch                  查看当前分支<br>
  git merge <name>            合并某个分支到当前分支<br>
  git branch -d <name>        删除分支<br>
  git branch -D <name>        强制删除分支<br>

#### 临时存储
> git stash                   把当前工作现场存储起来<br>
  git stash list              查看已存储<br>
  git stash apply             把已存储的内容恢复，内容不删除<br>
  git stash drop              删除已存储的内容<br>
  git stash pop               恢复内容以后同时删除内容<br>

#### 远程库
> git remote                  查看远程库信息<br>
  git remote -v               查看远程库详细信息<br>
  git push origin <name>      把该分支上的所有本地提交推送到远程库<br>
  git checkout -b <name> origin/<name>  在本地创建和远程分支对应的分支,本地和远程分支的名称最好一致<br>
  git branch --set-upstream <name> origin/<name>  建立本地分支和远程分支的关联<br>
  git pull                    从远程合并<br>
  如果提示no tracking information   
  则说明本地分支和远程分支的链接关系没有创建，可以使用
  git branch --set-upstream-to <name> origin/<name><br>

#### 打标签
> 标签总是和某个commit挂钩。
  git tag <tagname>           用于新建一个标签，默认为HEAD，也可以指定一个commit id<br>
  git tag <tagname> commit_id 给指定一个commit_id新建一个标签<br>
  git tag                     查看所有标签<br>
  git show <tagname>          查看标签信息<br>
  git tag -d <tagname>        删除一个本地标签<br>
  git push origin <tagname>   推送某个标签到远程（PS：创建的标签都只存储在本地）<br>
  git push origin --tags      一次性推送全部尚未推送到远程的本地标签<br>
  如果标签已经推送到远程，要删除远程标签就麻烦一点
  git tag -d <tagname>                   删除一个本地标签<br>
  git push origin :refs/tags/<tagname>   删除一个远程标签<br>

#### 小结
git是Linux之父linus创造的，属于一种分布式版本控制系统。（PS：区别于集中式的版本控制系统）