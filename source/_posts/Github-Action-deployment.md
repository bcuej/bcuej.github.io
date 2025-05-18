---
title: Github Action deployment
date: 2025-05-14 10:12:29
tags: tech
excerpt: 坚持优秀的学习习惯。
---

3月底就开始在部署，过了整整一个月才有了眉目。、

怎么说呢，还是学习习惯和态度的问题。其实并不是很难（~~事后诸葛亮~~），但是一直耽于不理解实现的逻辑和不了解实现的各处细节，导致之前的部署一直报错或者没能实现理想的功能。

优秀的学习过程需要产生理解，不管正确与否，理！解！

Github Action可以完成很多自动化部署。在触发条件后，github可以通过脚本将本应在本地运行的指令放到它的服务器上完成，这样的话就能减少我们的工作量。在博客部署方面它的表现就是把hexo手动部署的步骤和操作全部自动化，虽然博客的更新工作体量小，但是同样具有便捷性。

我的部署过程主要基于这篇文章：[GitHub Actions 来自动部署 Hexo](https://zhuanlan.zhihu.com/p/170563000)

# GitHub pages 与本地设置

```yml
deploy:
  type: git
  repo: git@github.com:bcuej/bcuej.github.io.git
  branch: gh-pages
```
因为我的博客静态文件存放在 gh-pages 分支，所以这里设置为 gh-pages，同时GitHub page也要保持一样的设置。
![alt text](GitHubpage.png)

# 配置密钥

## 生成公钥私钥
```
ssh-keygen -t ed25519 -C "your_email@example.com"

// 如果系统不支持 Ed25519 算法
ssh-keygen -t rsa -b 4096 -C "your_email@example.com"
```
在git bash执行上述命令过后，可以在本地（e.g. C:\Users\xxx\\.ssh）查看 id_rsa 和 id_rsa.pub 两个文件。

## 配置到GitHub

私钥：用于触发 GitHub Actions
公钥：用于博客部署的写入

因为我的博客源码和静态文件放在同一仓库中，所以只需要对同一个仓库进行设置即可。

### 私钥

<code> 仓库 -> Settings -> Secrets and variables -> Actions -> New repository secret </code>

然后将 <code>id_rsa</code>中的值添加到 <code>Secret</code> 中，并注意 <code>Name</code> 的命名（即 **DEPLOY_TOKEN**）要和后面的workflow中的key变量名称保持一致。

添加完成后be like:

![alt text](secretkey.png)

### 公钥

<code> 仓库 -> Settings -> Deploy keys -> Add deploy key </code>

Key中填写 <code>id_rsa.pub</code> 中的内容。

注意一定要勾选 <code>Allow write access</code>

# Workflow

在 <code>.github/workflow</code> 路径下添加 .yml 文件。

workflow文件的层级逻辑：

![alt text](workflowmindmap.png)

- workflow 文件命名

```yml
name: Deploy Hexo to GitHub Pages 
```

- 触发工作流

```yml
on:
  push:
    branches: [main] #选择要push的分支
```
当有人 push 代码到 main 分支时，就会触发执行这个 GitHub Actions 的工作流。而如果并未push到main分支时，就不会触发该工作流。 

📓 为什么是有人呢？
因为在 GitHub 大型项目中通常是多人协作，仓库中会有不同的提交或者拉取等操作。

- 具体 workflow 设置

```yml
jobs:
  hexo-deployment: #jobs命名
    runs-on: ubuntu-latest #指定运行的机器系统
    env:
      TZ: Asia/Shanghai #设置环境变量——时区
```

```yml
steps:
- name: Checkout source #每个step的名称
      uses: actions/checkout@v3 #每个step对应的action。
```
这些action可以从github提供的action插件获取。Action的命令已经编写好，直接引用即可。

## 完整的workflow 配置

```yml
name: Deploy Hexo to GitHub Pages

on:
  push:
    branches: [main]  # 注意这里是监听主分支（源代码分支），所以 push 后面填的是 main 分支

jobs:
  hexo-deployment:
    runs-on: ubuntu-latest
    env:
      TZ: Asia/Shanghai

    steps:
    - name: Checkout source
      uses: actions/checkout@v3

    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18' #版本18较为稳定

    - name: Install pandoc #这一段是博客某些功能所需，如果博客中不需要用到 pandoc 插件可不用安装。
      run: |
        sudo apt-get update
        sudo apt-get install -y pandoc

    - name: Install dependencies & Generate static files
      run: |
        npm i -g hexo-cli
        npm install
        hexo clean
        hexo generate

    - name: Deploy to GitHub Pages
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.DEPLOY_TOKEN }} #这里的secrets.DEPLOY_TOKEN 即是之前添加的私钥。
        publish_branch: gh-pages #这里是GitHub pages的推送分支。
        publish_dir: ./public
        keep_files: true

```

# 触发自动化部署

``` bash
git add . # 将改动的文件添加到暂存区
git commite -m "updating blog"
git push origin main

```
可以通过 git branch 查看仓库分支，绿色字体的为目前所在分支。git push origin xxbranch 能精准推送到你需求的分支。

📓 note 为什么是 origin ？
git clone https://github.com/username/repo.git
执行上述命令过后 GitHub 会把上述仓库默认命名为 origin


# 让我们试一试
Unfortunately, 出现了下述报错

![alt text](error.png)

报错显示出现了一个未知的块标签：unknown block tag: note

最简单的方法就是把该标签删掉，但是就会失去很多博客的可玩性。

我们选择安装依赖 <code>hexo-tag-note</code>

在 package.json 中的 dependencies 里添加该依赖
```json
"dependencies": {
  "hexo-tag-note": "^1.0.0",
}
```

让我们再试一次。

好的再次失败。因为原生主题可能不支持这个语法或者是因为GitHub提供的action中并不包含这个插件，所以我们还是选择舍弃一些趣味性吧。

删掉之后，我们再试一次。

又出现了新的报错。
![alt text](error3.png)

发现是 原本配置的公钥和私钥失效了。再重复之前的配置步骤即可。
![alt text](success.png)

# 部署成功但页面空白

## 问题
deployment 成功，但是打开博客时页面一片空白。

![alt text](blankpage.png)

发现 action 中有这样一段 warning：
![alt text](warn.png)

没有layout.html文件生成，导致页面不能正常渲染。

在根目录下的<code>_config.yml</code>中切换到默认的主题：<code>landscape</code>

```yml
theme: landscape
```

再次打开博客显示：
![alt text](landscape.png)

应该是主题文件夹路径的问题。

把 .gitmodules 删除了也没用。(因为之前有 .gitmodules 相关的warning, 大意是指找不到 themes/redefine 的子模块......对子模块的理解有差错。)

## 解决方法
继续debug，发现gitmodules还是有残留，并且 themes/redefine 文件并没有成功推送到仓库中，所以在触发action的时候出现了warning。

![alt text](gitmodule.png)

![alt text](redefine.png)

![alt text](gitmodulewarn.png)

让我们尝试一下从这个问题入手进行解决。

- 通过命令行移除子模块的设置。手动删除 .gitmodule 文件并不彻底，需要由命令行进行解绑。

```bash
git rm --cached themes/redefine
rm -rf .git/modules/themes/redefine
```

并删除 .gitmodule 文件，包括 themes/redefine 目录下的。

```bash
rm -f themes/redefine/.gitmodules
```

然后将 themes/redefine 文件夹添加到暂存区，提交到仓库。

```bash
git add themes/redefine
git commit -m "push themes/redefine to the main branch"
git push origin main
```

Then, problem solved! 😁😁😁

用 Github Action 就能正常完成hexo博客自动部署了，顺利白嫖 GitHub 服务器✌✌

## 关于 hexo 的理解

### 关于子模块

- hexo的主题有些是通过子模块引入的。子模块可以用引用GitHub仓库实现，并且要在 <code>.gitmodule</code>中用下述语法引用。

```git
[submodule ".deploy_git"] #名称
    path = .deploy_git #路径
    url = https://github.com/repo.io.git #仓库url
```

### 关于主题

- 博客主题是直接下载到本地的，就可以不用添加子模块。

我在搭建博客的时候就是这种情况。

### 关于文件夹

- public：它存放了博客的公开资源。hexo cl时会清除这个文件夹下的内容，通过hexo g生成。
- .depoly_git：是本地静态文件到github仓库的中转站。它会将public中的文件复制一份，并通过 hexo d推送到GitHub仓库中。通过下述代码设置推送的目标仓库和分支。

```yml
deploy:
  type: git
  repo: git@github.com:bcuej/bcuej.github.io.git
  branch: gh-pages
```