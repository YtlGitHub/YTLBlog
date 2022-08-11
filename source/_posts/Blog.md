---
title: 部署个人博客
date: 2021-07-27 17:32:12
tags: Blog
categories: Blog
---

欢迎来到部署个人博客[ytlBlog](https://ytlgithub.github.io/)

## 部署个人博客

### 一.安装环境
1.安装[Git](https://git-scm.com/)
2.安装[node.js](https://nodejs.org/en/)
3.当然你要有一个[github](https://github.com/)账号，没有的话赶快去注册一个吧

### 二.配置Github
1.建立Repository,建立与你用户名对应的仓库，仓库名必须为【your_user_name.github.io】
2.配置SSH-Key
3.创建密钥方法
创建密钥：在你的博客目录下右键弹框点击Git Bash Here 打开Git命令仓库输入如下命令
创建密钥命令：ssh-keygen -t rsa -C "1766842398@qq.com"  注释：这里的邮箱输入你自己的
查看创建的密钥命令：cat ~/.ssh/id_rsa.pub  注释：复制这些密钥到GitHub ssh里面 头像点击设置里面添加ssh
会产生两个密钥一个id_esa 是私钥一个id_rsa.pub 是公钥
	
### 三.安装Hexo
1.打开Git命令行，执行如下命令
$ npm install -g hexo

### 四.开始部署前提条件
1.在电脑中建立一个名字叫「Hexo」的文件夹（比如我建在了D:\Hexo），然后在此文件夹中右键打开Git Bash。执行下面的命令
$ hexo init
Hexo随后会自动在目标文件夹建立网站所需要的文件
2.然后（在 /D/Hexo下）执行npm install
$ npm install
会在D:\Hexo目录中安装 node_modules

### 五.生成博客
1.启动在浏览器访问http://localhost:4000/ # 这时可以看到Hexo已为你生成了一篇blog
$ hexo server
可以简化
$ hexo s  生成本地浏览静态码
按Ctrl+C 停止Server
2.新建一个文章
$ hexo new "文章名"
刷新本地网页http://localhost:4000/ 可以发现已生成了一篇新文章 "文章名"
3.删除生成的public
$ hexo clean
4.生成静态网页
$ hexo generate
可以简化
$ hexo g 
该命令执行完后，会在 D:\Hexo\public\ 目录下生成一系列html、css等文件
5.编辑文章
hexo new "文章名"会在D:\Hexo\source\_posts目录下生成一个markdown文件：My-New-Post.md
可以使用一个支持markdown语法的编辑器（比如 Sublime Text 2）来编辑该文件。

### 六.部署到Github
1.安装hexo-deployer-git插件，在站点目录下输入下面命令安装插件就好了，后在使用Hexo d命令就可以推送了
$ npm hexo-deployer-git
2.部署到Github前需要配置_config.yml文件，首先找到下面的内容
deploy:
    type:
然后将它们修改为
deploy:
    type: git
    repository: git@github.com:YtlGitHub/YtlGitHub.github.io.git  注释：这个是输入你自己的SSH密钥地址
    branch: main  注释：main是你自己的分支
3.把代码文件推送到Github上
$ hexo deploy
4.测试
当部署完成后，在浏览器中打开https://YtlGitHuborganization.github.io/YtlGitHub.github.io/ ，正常显示网页，表明部署成功。
本地静态网页：http://localhost:4000

### 七.生成漂亮的主题
#### 1.下载主题
前言：我们知道它自己默认主题是非常难看的，所以我们就要下载一个漂亮的主题，网上有很多主题可以下载，这里我以[hexo-theme-matery](https://github.com/blinkfox/hexo-theme-matery)主题作为演示
(1).点击[这里](https://codeload.github.com/blinkfox/hexo-theme-matery/zip/master)下载最新稳定版代码的master分支。解压后，将hexo-theme-matery文件夹复制到你themes的Hexo博客项目文件夹中，并重命名为matery，名字自己可以随便定义与下面配置主题名字一致就行。
(2).当然，您可以使用git clone下载到您的themes文件夹中。
git clone https://github.com/blinkfox/hexo-theme-matery.git
#### 2.配置主题
(1).修改你博客路径下_config.yml文件里面theme: 的值，后面的值修改为matery，注意matery就是上面主题自定义的名字
```yaml
theme: matery
```
(2).修改主题显示自己的信息_config.yml博客路径里面的_config.yml，如果您是中国用户，建议将language的值改为zh-CN。
```yaml
title: YtlBlog
subtitle: '欢迎来到Ytl的博客'
description: '网站描述'
keywords: '您网站的关键字,支持多个值'
author: Ytl
language: zh-CN  # en
timezone: ''
```
(3).其他修改建议_config.yml博客路径里面的_config.yml
请修改的价值url的_config.yml，以你的网站的主URL(例如http://xxx.github.io)
我们建议修改这两个值per_page是的倍数6，如：12，18等等，以便可在每个屏幕下显示良好的职位列表。
```yaml
index_generator:
  path: ''
  per_page: 12
  order_by: -date
per_page: 12
```
(4).其他配置请参考[这里](https://github.com/YtlGitHub/ytlblog/tree/main/themes/matery)
你下载的主题下面的README_CN.md有详细介绍
hexo\themes\matery\README_CN.md

### 评论
<!-- 来必力City版安装代码 -->
<div id="lv-container" data-id="city" data-uid="MTAyMC81MzYzNy8zMDExMA==">
	<script type="text/javascript">
   (function(d, s) {
       var j, e = d.getElementsByTagName(s)[0];
       if (typeof LivereTower === 'function') { return; }
       j = d.createElement(s);
       j.src = 'https://cdn-city.livere.com/js/embed.dist.js';
       j.async = true;
       e.parentNode.insertBefore(j, e);
   })(document, 'script');
	</script>
<noscript> 为正常使用来必力评论功能请激活JavaScript</noscript>
</div>
<!-- City版安装代码已完成 -->

