---
title: "Hugo 添加评论系统 utterances"
date: 2019-05-09T10:22:22+08:00
lastmod: 2019-05-09T10:22:22+08:00
draft: false
hidden: false
keywords: []
description: "Hugo 添加评论系统 utterances"
tags: ["hugo", "utterances", "github"]
categories: ["blog"]
author: ""
---
## 评论系统
虽然博客没有什么人看，但是还是要添加一下评论系统嘛，这样如果博客有什么问题被人指出的话，也很方便交流。

我使用的 hugo 主题是 even，功能非常齐全，界面也很好看。在配置文件中我们可以看到 even 主题支持的评论系统有：disqus，畅言，来必力，gitment，gitalk 等等。

我的选择标准是：

1. 最好是国外服务，因为国内服务通常来说稳定性不够(可能开到一半就跑路不支持了)，所以畅言就被排除了
2. 支持 markdown，所以 disqus 和 来必力 就被排除在外了

因此最终的选择就是基于 Github 的评论系统，even 支持的有 gitment, gitalk
<!--more-->
## 安全性问题
最开始打算采用 gitment，但是仔细调查一番之后发现 gitment 存在一定的[安全性问题](https://blog.wolfogre.com/posts/security-problem-of-gitment/)。主要有两点：

1. gitment 需要把 Client ID、Client Secret 以明文形式写进前端代码里
2. 注册时需要的权限过多，包括对所有公开仓库的读写权限

具体的安全风险可以点击链接查看。

## utterances 
继续调研，发现一个非常好的开源的基于 Github 的评论系统：[utterances](https://github.com/utterance/utterances)。

utterances 与 gitment 和 gitalk 的区别在于：

1. utterances 只需要读写一个 repo 的权限
2. utterances 使用 bot 来初始化评论，比 gitment 更方便
3. utterances 对用户授权来说非常简单

它的原理是，设置一个用于放评论的 repo，然后对博客中的每一篇文章都有一个 issue，每个人的评论放在里面。

但是 even 主题并不支持 utterances，所以需要我们手动整合进入主题。

### 在even 主题中加入 utterances
首先需要去 Github 上新建一个 public repo，名字无所谓，我取的是 `blog-comment`，然后记下这个仓库名字，在博客中进行配置。

为了方便配置，我们把需要的参数都放在 `config.toml` 文件中，在该文件中加入：
```toml
[params.utter]
    repo = "{githubName}/{repoName}"
    issueTerm = "title" #设置每篇文章对应的 issue 的名字，可选 pathname title url，
    theme = "github-light"
```
接着打开 `themes\even\layouts\partials` 文件夹，我们可以看到很多 html 模板，我们需要改动的是 `comments.html`，

文件内容的结构是非常清楚的，我们可以在 gitalk 下面加一个 utterances 的控制结构：
```html
{{- if .Site.Params.utter.repo -}}
    <div id="utter-container"></div>
    <script src="https://utteranc.es/client.js"
        repo= '{{ .Site.Params.utter.repo }}'
        issue-term= "{{ .Site.Params.utter.issueTerm }}"
        theme= '{{ .Site.Params.utter.theme }}'
        crossorigin= "anonymous"
        async>
    </script> 
  {{- end }}
```

这样，我们就完成了 utterances 的配置。

## 参考链接
1. [更好的基于 github issues 的评论系统——utterances](https://www.xianmin.org/post/utterances-comment-system/)
2. [Gitment 的安全性争议](https://blog.wolfogre.com/posts/security-problem-of-gitment/)