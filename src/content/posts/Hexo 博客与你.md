---
title: Hexo 博客与你
published: 2025-02-09
tags: [GitHub, 教程]
abbrlink: hexo-blog-and-you
---

## 前言

如果你还在忍受诸如新浪微博、腾讯微博等博客网站的臃肿，那你马上该读一读这篇文章了。

拥有一个你自己的博客网站，你可以：

* 记录日常生活⏰
* 写写学习笔记📋
* 跟其他博客创作者们探讨写作🖊
* ~~发电⚡~~

至于为什么写博客，上知乎搜一搜就有很多答案。我的语文老师说过一段话，我觉得很适合回答这个问题：

> ~~作文~~博客是孩子们的一种创造，是一种优秀的表现，古人曾经靠的是一篇文章走遍天下。而如今，能写得一手好文章，也会受益终生。谢谢。

那不多废话，往下读开始你的博客生涯吧。

## 准备工作

在开始之前，你应该：

* 有一台能访问互联网的，能正常工作的电脑。
* 会使用搜索引擎。知道常用计算机术语并至少有初中的英语水平。
* 会基础的文件操作。如复制、粘贴、下载和上传。
* 有一个能正常使用的电子邮箱。如 QQ 邮箱。

另外，我非常建议使用加速器和 VPN。因为 GitHub 在中国没有服务器，使用 VPN 能让你快速访问网站。

## 搭建环境

**务必按照顺序来安装！**

### Ⅰ 下载 git

从 [git 官网](https://git-scm.com/ "快点我！")下载对应的安装包：

![看红色的框框](https://pan.moe/f/0vWHQ/GitWebsite.png "GitWebsite")

安装好后在桌面 右键 → Open Git Bash here 输入 ``git --version`` 查看，这样显示就算安装成功了：

![就像这样](https://pan.moe/f/1b2f7/GitVersion.png "GitVersion")

打开你下载 git 的目录，右键 git-bash.exe 选择[属性]并如下选择：

![如果不这么做，后续上传会出问题！](https://pan.moe/f/BvvFr/GitAdmin.png "GitAdmin")

### Ⅱ 下载 Node.js

从 [Node.js](https://nodejs.org/en/download/ "快点我！") 官网下载对应的安装包：

![看红色箭头](https://pan.moe/f/X6eF1/NodejsWebsite.png "NodejsWebsite")

在 Git Bash 中输入 ``node -v``，不报错即可。

环境配置参考[这里](https://cloud.tencent.com/developer/article/2037057 "快点我！")。

### Ⅲ 安装 Hexo

在 Git Bash 中输入 ~~`npm config set registry https://registry.npm.taobao.org`~~（这一步换下载源，是为了后面下载不报错）。输入``npm install -g hexo``，等待完成后，输入``hexo -v``。

更正：淘宝的镜像源已经修改为 `https://registry.npmmirror.com`，请参照本教程的朋友及时修正。

![这样显示就对了](https://pan.moe/f/DrEH1/HexoVersion.png "HexoVersion")

继续输入 ``npm install --save hexo-deployer-git``安装依赖。

**到这里，全部环境就搭建好了，下面是正式的搭建博客环节。**

# 使用 git 生成 SSH key

**如果你的用户名称中含有中文，这一步不建议在 Git Bash 中操作。``Windows``自带搜索框搜索``cmd``，右键→以管理员身份运行是更好的选择。**

如果你到现在都没有自己的 GitHub 账户，就现在去[注册一个](https://github.com/join "快点我！")并设置好用户名。

在 Git Bash 或 cmd 中输入``ssh-keygen -t rsa -C "填你注册 GitHub 的邮件地址"``，按三次回车后，会像这样：

![生成后的页面像这样](https://pan.moe/f/KWgs4/SSHKEY.png "SSHkey")

然后输入``clip < ~/.ssh/id_rsa.pub``，将刚刚生成的 SSH 密钥复制到剪贴板。如果是 cmd，就把``~/.ssh/id_rsa.pub``改成 id_rsa.pub 的绝对路径。

接着打开 GitHub 主页，点击个人设置，点击左侧的 SSH and GPG keys，点击 New SSH key。

将你的剪贴板里的东西复制进去，Title 随意取：

![在 GitHub 输入](https://pan.moe/f/8MRik/AddKey.png "sshAddKey")

输入``ssh -T git@github.com``测试，接着输入``yes``，不报错即可。接着设置你的个人信息：

    git config --global user.name "karlbaey" # 你的github用户名
    git config --global user.email "xxx@example.com" # 填写你的github注册邮箱

# 搭建博客

### 新建博客

开始之前先介绍一下 Hexo 的基本命令

    hexo new "postName" # 新建文章
    hexo new draft "draftName" # 新建草稿
    hexo new page "pageName" # 新建页面
    hexo generate # 生成静态页面至 public 目录
    hexo server # 开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
    hexo deploy # 部署到GitHub
    hexo help  # 查看帮助
    hexo version  # 查看Hexo的版本

对应的缩写，比如：

    hexo n == hexo new
    hexo g == hexo generate
    hexo d == hexo deploy
    hexo s == hexo server


新建一个保存博客的存放目录，必须是空文件夹。比如卡尔白的是：``E:\Karlblogs``

在你的存放目录**右键→ Open Git Bash here**，输入命令``hexo init``

查看你的存放目录，里面应该出现了这些：

![不需要完全相同，大体一致即可](https://pan.moe/f/p0jsQ/KarlblogsFolder.png "KarlblogsFolder")

> 生成静态网页并预览:
> `hexo g && hexo s`
> 浏览器输入``http://localhost:4000``
> ![预览界面](https://pan.moe/f/WAZCk/HexoPreview.jpg "Preview")
------
> **报错解决：**
> 问题：hexo g 报错，`line.mathALL is not function` 问题解决
> 原因：nodejs 版本低于12
> 解决：两种方法
>
> 1. 请将 nodejs 升级到高于12.0.0的版本
> 2. _config.yml 中的 highlight->enable 的值从 `true` 更改为 `false`，这样可以避免异常。

访问没问题了，接下来部署到 GitHub。

### 部署到 GitHub

**注意：** 此处需要一个新仓库（`Repository`），仓库名称格式：``用户名.github.io``。比如卡尔白的就是：`karlbaey.github.io`。

![](https://pan.moe/f/wOZiz/NewRepo.png "NewRepo")

![因为我已经注册过了，就不可以再注册一个](https://pan.moe/f/6ekh1/RepoName.png "RepoName")

编辑`_config.yml`，`_config.yml`在博客存放目录下：

```yaml
deploy:
  type: git
  repository: git@github.com:Owner/Repo
  branch: main
  message: Commonly updation
```

repository 仓库地址改为自己的。branch 看自己的 GitHub 仓库是 master 还是 main，卡尔白这里是 main，所以就填写 main。message 填上你希望的提交信息[^1]。

![看红色框框](https://pan.moe/f/V6MUR/KarlblogsRepo.png "KarlblogsRepo")

发布到 GitHub，命令：``hexo d``。

在浏览器访问：`https://karlbaey.github.io/`

不出意外即可成功访问。

> 这个博客地址已经是部署到了公网中的 `karlbaey.top`，感兴趣的读者也可以访问。

### 编写博客

搭建好博客之后，日常中肯定是需要编写博客并且同步到 GitHub。卡尔白来带大家写一篇博客。

新建一篇名为``Karl-first-blog``的博客：``hexo n "Karl-first-blog"``

查看对应的文件夹：

![新产生的 Markdown 文件](https://pan.moe/f/kBduq/Karlfirstblog.png)

可以看到生成了一个``Karl-first-blog.md``文件，通过这个 Markdown 文件去写博客。（这里用的是 VSCode 来编辑，也推荐用 Typora 或 Obsidian 等专用富文本编辑器来写）

![新产生的 Markdown 文件内的样子](https://pan.moe/f/dnBt5/InKarlfirstblog.png)

默认的内容是这样的：

```markdown
---
title: Karl-first-blog
date: 2025-02-10 23:47:31
tags:
---
这里开始编写正文（markdown 格式）
```

使用 markdown 格式编写好后，在博客存放目录**右键→ Open Git Bash here**，输入``hexo g && hexo d``，你的博客就被部署到网站上了。访问网站就能看到。

## 结语

到这里，你就应该会写博客和部署博客了，（如果有）下一期就告诉诸位怎么导入模板和网站操作。

完结撒花！🎇🎇🎇

## 脚注

[^1]: 后来在把博客扁平化的过程中，我把博客仓库更名为 [Karlblogs](https://github.com/Karlbaey/Karlblogs)，分支也从 main 改成了 master。这不影响使用。现已清空库存后存档。

