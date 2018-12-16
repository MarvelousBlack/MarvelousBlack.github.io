---
title: "用hugo搭建一个博客"
date: 2018-11-30T20:24:52+08:00
description: "blog 刚刚建起来，那就记录一下建立的过程吧。"
topics:
  - 新手教程
  - 杂乱的笔记
tags:
  - blog
  - hugo
draft:  false
---
距离上次写有一个星期了，嘛，这次就来写写怎么搭个 blog 好了。（不完全笔记

## 0x00 准备
工欲善其事必先利其器。搭建一个 blog 你需要准备：

- 一个空间用来存放你的blog
- hugo
- 简单的 markdown 语法知识

## 0x01 关于hugo和安装
hugo是最受欢迎的开源的静态页面生成器，用go语言书写的。拥有令人令人惊讶的速度和可塑性。可以让你更加专注于写作，而且拥有大量十分好看的主题可选。 那么要怎么安装呢？  
在 archlinux下只需要一句 `pacman -Syu hugo`就好了。窗户的话，只需要去 [官网](https://gohugo.io/) 下载对应的可执行文件就好了。

## 0x02 开始创建并装饰你的网页
### 创建网页文件
创建一个网页文件很简单只需要
```bash
hugo new site （文件夹名称）
```
然后你就可以在当前的工作目录下找到一个你刚刚新建的文件夹。
### 安装主题
hugo生成的默认目录是不带主题的，这挺适合我这种喜欢自己配置的。进入刚刚创建的目录，在[这里](ihttps://themes.gohugo.io/)选择一个你喜欢的主题。 **注意：每个主题都是不一样的，请仔细阅读对应主题的 readme 文档** 惠狐之书使用的是 hyde-Y 主题，~~这个主题看起来有点像比特垃圾桶~~ , 所以下文将以这个主题为示例。  
按照主题写的命令
```bash
$ cd your_site_repo/
$ mkdir themes
$ cd themes
$ git clone https://github.com/enten/hyde-y
```
这样就安装好主题了，但还没开始配置
#### 主配置文件
编辑 `config.toml` (如果没有则新建）按照下面的模板开始修改。
```file
# hostname (and path) to the root eg. http://spf13.com/
baseurl = "http://www.example.com"

# Site title
title = "sitename"

# Copyright
copyright = "(c) 2015 yourname."

# Language
languageCode = "en-EN"

# Metadata format
# "yaml", "toml", "json"
metaDataFormat = "yaml"

# Theme to use (located in /themes/THEMENAME/)
theme = "hyde-y"

# Pagination
paginate = 10
paginatePath = "page"

# Enable Disqus integration
disqusShortname = "your_disqus_shortname"

[permalinks]
    post = "/:year/:month/:day/:slug/"
    code = "/:slug/"

[taxonomies]
    tag = "tags"
    topic = "topics"

[author]
    name = "yourname"
    email = "yourname@example.com"

#
# All parameters below here are optional and can be mixed and matched.
#
[params]
    # You can use markdown here.
    brand = "foobar"
    topline = "few words about your site"
    footline = "code with <i class='fa fa-heart'></i>"

    # Sidebar position
    # false, true, "left", "right"
    sidebar = "left"

    # Text for the top menu link, which goes the root URL for the site.
    # Default (if omitted) is "Home".
    home = "home"

    # Select a syntax highight for highlight.js
    # Check the static/css/highlight directory for options.
    # Leave unset to fall back to default hugo highlighter instead of highlight.js
    highlight = "default"

    # Google Analytics.
    googleAnalytics = "Your Google Analytics tracking code"

    # Sidebar social links.
    github = "enten/hugo-boilerplate" # Your Github profile ID
    bitbucket = "" # Your Bitbucket profile ID
    linkedin = "" # Your LinkedIn profile ID (from public URL)
    googleplus = "" # Your Google+ profile ID
    facebook = "" # Your Facebook profile ID
    twitter = "" # Your Twitter profile ID
    youtube = ""  # Your Youtube channel ID
    flattr = ""  # populate with your flattr uid
    flickr = "" # Your Flickr profile ID
    vimeo = "" # Your Vimeo profile ID

    # Sidebar RSS link: will only show up if there is a RSS feed
    # associated with the current page
    rss = true

[blackfriday]
    angledQuotes = true
    fractions = false
    hrefTargetBlank = false
    latexDashes = true
    plainIdAnchors = true
    extensions = []
    extensionmask = []

```
上面都有注释就不解释了。接着是配置侧边栏。  

#### 侧边栏
创建`data/Menu.toml`这个文件，下面是示例（不需要删掉就好了

```
[about]
    Name = "About"
    URL = "/about"

[posts]
    Name = "Posts"
    Title = "Show list of posts"
    URL = "/post"

[tags]
    Name = "Tags"
    Title = "Show list of tags"
    URL = "/tags"
```
#### 页脚链接
创建`data/FootMenu.toml`不需要可以跳过这一步
```
[license]
    Name = "license"
    URL = "/license"
```
#### 完整页面
接着是创建刚刚侧边栏的 about 和页脚的 license 了。
```
$hugo new about.md
$hugo new license.md
```
然后进入 `content` 目录编辑刚刚创建的两个文件。文件里面使用 markdown 语法。关于 markdown 的语法，请自行谷歌。
#### 网页图标
这个主题支持 favicon 只需要放入 `static/favicon.png` 和 `static/touch-icon-144-precomposed.png` 这两个文件就好了。

## 0x03 开始写作
创建新的文章只需要使用 
```bash
$ hugo new post/xxx.md
```
这样就会自动创建一个新文件，接着你需要编辑`content/post/`下面刚刚创建的文件。开始愉快的写文章吧！  
你可能需要添加一些 tag 之类的，这个很简单只需要在文件一开始写成像下面这样：
```file
---
title: "用hugo搭建一个博客"
date: 2018-11-30T20:24:52+08:00
description: "blog 刚刚建起来，那就记录一下建立的过程吧。"
topics:
  - 新手教程
  - 杂乱的笔记
tags:
  - blog
  - hugo
draft:  false
---
```
这样就可以添加 tag 和 topics 。这里的 draft 项是草稿，当后面为 ture 的时候，这个文章是不会出现在最后的页面里面的。但在预览的时候可以看见。

## 0x04 预览和生成站点
#### 预览
预览的话只需要
```bash
$ hugo server -D
```
这样就可以在自己的机器上预览文章的效果了
#### 生成
生成的话命令就更简单了
```bash
$ hugo
```
最后生成的文件在 `public/` 下面。 

## 0x05 发布（最后一步）
发布的话你只需要把，这一个 `public` 文件夹里面的内容全部复制到你空间对应的地方就好了。 

## 0x06 剩下的事情
设置评论区什么的，还有让谷歌百度收录之类的。那些就不再本文中介绍了，请参看对应网站的说明。最后感谢你把此文看到最后，如有疑问可以直接在评论区回复。汝如果发现文章有误，请联系我，我会及时更改的。
