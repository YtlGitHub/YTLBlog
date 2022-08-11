---
title: git
date: 2021-07-25 22:52:08
tags: git
categories: git
---
## git命令
### 一.安装git
#### Linux上安装：
命令：yum install git
验证安装是否成功命令:git --version
#### Windows上安装：
[Git](https://git-scm.com/)官网直接下载安装程序，然后默认安装即可
安装完之后桌面右击会出现两个图标分别是：Git GUIhere 和 Git Bash Here
一般习惯性用git bash here
最后一步设置标识，输入如下命令：
git config --global user.name "你的名字"
git config --global user.email "你的邮箱"
### 二.git流程
```text
Remote---pull------------------------------------------------->workspace
Remote---fetch/clone--->Repository---checkout----------------->workspace
Remote<--push-----------Repository<---commit---index<---add<---workspace
* workspace:工作区
* index/stage:暂存区
* repository：本地仓库
* remote；远程仓库
```
### 三.如何使用
#### 1.创建或者获取版本库
创建本地库，选择一个文件目录，然后右击git bash命令行窗口，执行如下命令初始化一个本地仓库
```gitbash
git init  # 在当前目录初始化
git init "新仓库名"  # 创建一个新的文件夹做仓库
```
#### 2.获取远程仓库至本地仓库
```gitbash
git clone git://github.com/YtlGitHub/YtlGit
```
这里获取远程仓库我用的是ssh密钥的方式，获取密钥方式如下
```gitbash
ssh-keygen -t rsa -C "176@qq.com"  # 这里输入你自己的邮箱
cat ~/.ssh/id_rsa.pub  # 查看生产的密钥
```
id_esa 是私钥
id_rsa.pub 是公钥
在GitHub 头像点击设置里面添加ssh
#### 3.检查当前文件状态
```gitbash
git status
```
#### 4.把更改的内容添加到暂存区
三种方式添加
```gitbash
git add filename  # (指定文件名)
git add *  # (所有文件)
git add *.txt  # (支持通配符，所有.txt文件)
```
此时，暂存区的内容和工作区的内容一致
#### 5.提交到本地仓库
```gitbash
git commit -m "代码提交信息"
```
注意：每次提交前，先用git status看下，是不是都已暂存起来了，然后在执行提交命令
#### 6、跳过暂存区域的提交方法
```gitbash
git commit -a -m "代码提交信息"
```
git commit 加上-a 选项，git 就会把自动把所有已经跟踪过的文件暂存起来一并提交。从而跳过add
#### 7.查看工作区和版本库里面最新版本的区
```gitbash
git diff HEAD -- filename
```
#### 8.移除文件：
```gitbash
git rm filename
```
#### 9.对文件重命名
```gitbash
git mv README.*** README
```
(这个命令相当于 mv README.*** README、git rm README.***、git add README 这三条命令的集合)
#### 10.查看提交历史
```gitbash
git log
git log --pretty=oneline
```
#### 11.版本回退
```gitbash
git reaet --hard HEAD^  # 回退到上一个版本
git reaet --hard HEAD^^  # 回退到上上版本
git reaet --hard HEAD~100  # 回退到前100个版本
git reaet --hare HEAD 版本号  # 回退到固定版本号
git reflog  # 记录每一次命令(可查看版本号)
```
#### 12.与远程仓库建立连接
```gitbash
git remote add origin git@github.com:YtlGitHub/YtlGit.git  # 自己仓库的ssh路径
```
#### 13.如果已经关联了远程仓库，可以使用下述命令查看远程仓库是哪个
```gitbash
git remote -v
```
#### 14.然后将本地的改动提交到远程仓库
```gitbash
git push origin 分支名
```
#### 其他分支提交方法
```gitbash
15、创建一个分支dev
    git branch dev
    git branch 后面不加分支的名字就是查看当前分支
16、切换到当前分支
    git checkout dev
17、创建并切换分支
    git checkout -b dev
18、切换到主分支
    git checkout master
19、合并dev分支到master(可能会有冲突)
    git merge dev 或
	git merge dev --allow-unrelated-histories
20、把刚才新建的分支删除
    git branch -d dev
21、将分支推送到远端仓库（推送成功后其他人可见）
    git push origin 分支名
    git remote set-url origin https://github.com/YtlGitHub/YtlGit.git
    git push origin main
22、如果push失败了，那就先pull一下，在push
    git pull
```









