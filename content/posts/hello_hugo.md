---
title: "Hello Hugo"
date: 2022-11-02T12:07:31+08:00
draft: false
description: "Hugo的简介以及archie主题的使用与扩展"
tags: ["入门"]
---

## 前言
书写是一个深入理解知识的过程，在书写过程中总会发现自己对某一块儿知识认识的不足，从而有目标的去查资料更透彻的理解相关的知识。同时书写也是知识系统化的过程，在书写过程中总结片段知识，串珠成链，查漏补缺，增强知识的完整性。

## 关于静态网站

现在也有很多的平台可以帮助书写，有Medium、简书、知乎、掘金、微信公众号等这些完整的平台，也有需要自己从头搭建的平台，比如github pages等。这两种方式如果比较，都可以找到许多的优点和缺点，但是本质上没有什么大的区别。选择使用github pages也只是自己当下的一种选择，也许明天就会将这些内容发布到其他的平台上，也未必不可。

有许多的工具可以帮助我们在github pages上搭建静态网站，包括jekyll、hexo、vuepress等，也包括我使用的hugo。犹记得```gem install jekyll```时，被依赖关系整的焦头烂额，以及安装hexo```npm install hexo-cli -g```以及它们的theme的时候代理下载node_modules是更是让人心力憔悴。

而hugo的一个有点是安装方便，一个命令```brew install hugo```即可解决，转换theme也是```git clone```下载一个repo的小事，真是干净利索。另外hugo也有很多好看的theme可用，包括[hugo-theme-even](https://github.com/olOwOlo/hugo-theme-even)、[hugo-theme-cleanwhite](https://github.com/zhaohuabing/hugo-theme-cleanwhite)、[hugo-paper](https://github.com/nanxiaobei/hugo-paper)、[hugo-PaperMod](https://github.com/adityatelange/hugo-PaperMod)等，更多的theme可以到[Hugo theme](https://themes.gohugo.io/)网站和Github上搜索[Hugo theme Topics](https://github.com/topics/hugo-theme)，甚至直接Google也能搜到很多的好用的theme，都可以拿来自己调整。

下面就写一下自己使用的[archie](https://github.com/athul/archie)主题的调整，以及一些不满意的地方。

## Hugo的安装与使用
### Max下hugo的安装
```shell
┌─[codeclabs.cn] - [~]
└─[$] brew install hugo
```
### 查看hugo的版本信息
```shell
┌─[codeclabs.cn] - [~]
└─[$] hugo version
hugo v0.104.3+extended darwin/arm64 BuildDate=unknown
```

### 创建新的网站
```shell
┌─[codeclabs.cn] - [~]
└─[$] hugo new site codeclabs-cn.github.io
```

### 创建新的文章

```shell
┌─[codeclabs.cn] - [~/codeclabs-cn.github.io]
└─[$] hugo new posts/hello_hugo.md
```

文章默认放在content目录下。hugo默认使用`archetypes/default.md`文件作为模板创建文章，可以自行修改。

### 实时预览

```shell
┌─[codeclabs.cn] - [~/codeclabs-cn.github.io]
└─[$] hugo server -D
```

这里的`-D`选项，表示对于标记为draft文章也进行预览。

### 切换主题

切换主题分两步

1. 从github下载主题，并放到themes目录下，一般作为submodule添加进去

```shell
┌─[codeclabs.cn] - [~/codeclabs-cn.github.io]
└─[$] git submodule add https://github.com/athul/archie.git themes/archie
```

2. 修改config.toml

```shell
┌─[codeclabs.cn] - [~/codeclabs-cn.github.io]
└─[$] vim config.toml
theme="archie"
```

## 主题(Theme)

好看的theme千千万，选择一个自己看着舒服的即可。我试用过多个主题，最后选择了[archie](https://github.com/athul/archie)。

总结起来就是有以下几个方面的优缺点

### 优点：

- 支持light和dark切换
- 主题结构相对简单，修改起来比较简单
- 颜色搭配比较舒服，排版比较简洁

### 缺点：

- 缺失404页面
- light/dark切换按钮样式不统一
- 文章的标题和段落统一使用#进行分割，标题和正文也没有进行区分，导致看起来结构不清晰
- 没有文章大纲帮助导航，长一点的文章不容易在不同的部分进行跳转
- 只能放一些不成系统的blog，难以展现系统性的文章

有一些不满意的地方，现在就可以优化，还有一些地方需要后续进行升级。

### 已做的优化：

- 优化了404页面，增加了背景图片，添加了文案
- 修改了light/dark切换按钮，保持和其他菜单按钮样式一致
- 修改了subtitle的css类，使之能居中对齐
- 添加了[favicon.ico](http://codeclabs.cn/favicon.ico)
- 修改了`archetypes/default.md`，增加了`description`和`tags`选项。使用`description`作为文章的summary，而不是让hugo自己生成。文章通过`tags`选项而不是目录进行分类
- config.toml设置
- 添加`nableEmoji = true`支持emoji
- 添加`hasCJKLanguage = true`，支持中文
- 添加`[params] mode="toggle"`，支持手动切换light/dark模式
- 添加`[params] subtitle`，支持居中显示内容
- 添加markup设置，支持`<br>`换行

```shell
[markup]
  defaultMarkdownHandler = "goldmark"

[markup.goldmark]
  [markup.goldmark.renderer]
    unsafe = true
```

### 未来的升级

- 在文章右边添加[大纲](https://hugo-theme-even.netlify.app/post/even-preview/)支持
- 添加[book](https://hugo-book-demo.netlify.app/)/[doc](https://www.docsy.dev/)/[slides](https://rcjach.github.io/hugo-webslides/#slide=1)等类型的页面支持

## 问题

1. 在本地使用hugo可以预览，但是部署到github pages的时候确看不到文章？

   * 将文章开头的`draft:true`改为`draft:false`。文章默认的状态是`draft:true`，即草稿。在本地的时候有可能是使用`hugo server -D`进行预览的，其中的`-D`选项表示预览草稿。但是部署到github pages的时候，是不会发表`draft:true`的文章的，所以看不到相关的文章。

2. 文章有些地方不能换行，使用`\n`，`<br>`的都不行？

   * 在config.toml中添加[markup的设置](https://discourse.gohugo.io/t/goldmark-hard-line-breaks-in-a-table-cell/23400/5)

   ```
   [markup]
     defaultMarkdownHandler = "goldmark"
   
   [markup.goldmark]
     [markup.goldmark.renderer]
       unsafe = true
   ```

3. 文章的summary很长，并且将代码也当做summary的一部分显示出来了，很不好看？
   * 在config.toml中添加`summaryLength = 50`限制summary的长度，或者不使用hugo自动summary而在post的开头添加`description="文章的summary"`。个人建议在文章开头添加`description`描述，自己用简练的话总结这篇文章的内容，内容显示可以自己控制，也可以帮助自己梳理精进自己的文章。

## 总结

本文记录一些对hugo和archie的一些思考，期待后面能给大家呈现更多有价值的思考和文章。

![img](/img/posts/hello_hugo/people_on_unnamed_hill.png)
