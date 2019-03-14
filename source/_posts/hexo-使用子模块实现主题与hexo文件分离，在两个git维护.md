---
title: hexo 使用子模块实现自定义主题与hexo文件分离，在两个git维护
date: 2019-02-09 11:47:12
tags: hexo
category:
- Study
- Coding
id: 6
---

以前我们已经设置并发布了基于hexo的博客。文章源代码和hexo配置文件在git版本控制下。
现在我们想把整个本地hexo的配置文件等等全部托管到远端仓库，相当于备份。但是git add . 不会把我们hexo里面的themes目录下我们自己设置的主题同步上去，因为这个主题可能是我们从别的地方clone或下载的，包含了别人的git信息。因此本文来实现将别人的themes托管到远端仓库，实现自定义主题与hexo文件分离，在两个git维护。
<!--more-->

## 配置主题，错误的方式

与大多数hexo教程一样，下一步是更改默认主题。有很多主题可供选择。但是，大多数主题只是要求您在themes文件夹下克隆自己。

在这种情况下，我们将使用流行的主题NexT。其文件指定如下：

- 安装

>1. git clone https://github.com/iissnan/hexo-theme-next themes/next

- 更新_config.yml以使用此主题

>1. theme: next

- 在需要时拉主题更新

>1. cd theme/next
>2. git pull

## 问题

对于大多数主题（包括NexT），我们必须修改主题文件夹下的主题_config.yml文件，以便：

- 更改主题级别的配置，例如更改主题样式
- 更新个人信息
- 设置Google Analytic密钥
- 与第三方评论插件集成
- 未来以您想要的方式定制主题

当然，您希望在版本控制下进行上述修改，因此您可以检查它们并推送到Git。

现在，如果您前往Github源代码页，您会发现一个名为“Next”的有趣的灰色文件夹。

![github中的灰显文件夹](http://feng.lu/2016/08/12/How-to-setup-a-hexo-based-blog-Part-2/grayedout-folder-in-github-without-ref.png)

您也失去了在那里浏览主题源代码的可能性。

## 根本原因

从技术上讲，我们创建了一个**嵌套的git存储库结构**：git存储库“Blog-Hexo”下面有一个git存储库“Next”。这个[git文档](https://git-scm.com/book/en/v2/Git-Tools-Submodules)对此有一个很好的解释。

## 正确的方式

由于我们的自定义主题成为博客系统的独立组件，因此需要对其进行适当的版本控制。

1. Fork主题项目
通过这样做，您可以正确地控制对主题的修改，但仍然可以使其保持最新状态。fork版本是https://github.com/linkcd/hexo-theme-next。

2. 创建名为“next-linkcd”的Git子模块（意味着它是我们自己的NexT主题版本），并指向https://github.com/linkcd/hexo-theme-next。
    >1. cd blog-hexo 
    >2. git submodule add https://github.com/linkcd/hexo-theme-next themes/next-linkcd

3. 请求hexo使用我们自己的主题
    >1. theme: next-linkcd

4. 现在您有2个独立的存储库，一个用于您的blog-hexo，另一个用于您自己的主题。您可以运行以下命令来查看关系。

![命令行中的next-linkcd git子模块](http://feng.lu/2016/08/12/How-to-setup-a-hexo-based-blog-Part-2/Next-linkcd%20git%20submodule%20command%20line.png)
它也适用于Github网页

![Github中的Next-linkcd git子模块](http://feng.lu/2016/08/12/How-to-setup-a-hexo-based-blog-Part-2/Next-linkcd%20submodule%20in%20github.png)
请注意，两个地方的“git戳”是d82e379。

## 自定义主题

现在您可以自由修改主题了。要将主题更改推回Github，请按照以下步骤操作：

1. Git添加，提交并推入next-linkcd文件夹
2. Git添加，提交和推送blog-hexo文件夹，即使您没有修改博客中的任何内容。
   
### 补充
在实际使用的时候，理论上会有3个仓库，一个是我们搭建的hexo博客仓库，一个是我们备份的本地hexo仓库，另一个是这个仓库的子模块（submodule），也就是我们fork的主题仓库。
使用时，我们会对主题进行或多或少的修改，因此修改后的主题我们可以通过git同步到远端仓库。本地备份的hexo仓库一般改动不大，用git备份到远端仓库即可。平时写文章我们搭建的博客会改动，所以需要hexo clean，hexo s来推送到远端git仓库。

## 两台PC之间同步

>1. cd blog-hexo 
>2. git pull 
>3. git submodule update

## 在新PC中恢复您的博客

1. 从Github恢复源代码

>1. git clone  https://github.com/linkcd/blog-hexo.git
>2. cd blog-hexo 
>3. git submodule update --init

![恢复博客](http://feng.lu/2016/08/12/How-to-setup-a-hexo-based-blog-Part-2/Restore-blog.png)

2. 重新安装hexo程序。
无需重新安装插件。

>1. cd blog-hexo 
>2. npm install hexo 
>3. npm install

3. 完成



**本文参考fenglu大佬的文章，[原文](http://feng.lu/2016/08/12/How-to-setup-a-hexo-based-blog-Part-2/)为英文，自己翻译过来，并加入自己使用时遇到的问题以及分析过程总结，侵删转注**