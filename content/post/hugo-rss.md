---
title: "Hugo 博客 Rss 全文输出设置"
date: 2019-05-10T15:42:45+08:00
lastmod: 2019-05-10T15:42:45+08:00
draft: false
hidden: false
keywords: []
description: "Hugo 博客 Rss 全文输出设置"
tags: ["hugo","rss"]
categories: ["blog"]
author: ""
---

因为平常就用 rss 订阅新闻的习惯，所以也想订阅一下自己的博客，even 这个主题直接在页面下面就有 rss 订阅链接的按钮。但是点进去之后发现输出的 rss 内容只有摘要的部分，这不正是我平常最反感的那类订阅吗(WTF)。所以...直接搜索一下解决方法吧。

<!--more-->

## Hugo 全文 RSS 输出
[这里](https://xuanwo.io/2018/04/08/hugo-rss-output-all-content/)有一篇很好的解决方法。这个博客的博主相当厉害，令人感到羞愧。从他的博客里我学会了:

1. RTFM is an initialism for the expression "read the fucking manual".
2. 增加全文 RSS：
    在 `themes\even\layouts` 文件夹下新建一个 `index.rss.xml` 文件，内容为：

```xml 
<rss version="2.0" xmlns:atom="http://www.w3.org/2005/Atom">
  <channel>
    <title>{{ if eq  .Title  .Site.Title }}{{ .Site.Title }}{{ else }}{{ with .Title }}{{.}} on {{ end }}{{ .Site.Title }}{{ end }}</title>
    <link>{{ .Permalink }}</link>
    <description>Recent content {{ if ne  .Title  .Site.Title }}{{ with .Title }}in {{.}} {{ end }}{{ end }}on {{ .Site.Title }}</description>
    <generator>Hugo -- gohugo.io</generator>{{ with .Site.LanguageCode }}
    <language>{{.}}</language>{{end}}{{ with .Site.Author.email }}
    <managingEditor>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</managingEditor>{{end}}{{ with .Site.Author.email }}
    <webMaster>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</webMaster>{{end}}{{ with .Site.Copyright }}
    <copyright>{{.}}</copyright>{{end}}{{ if not .Date.IsZero }}
    <lastBuildDate>{{ .Date.Format "Mon, 02 Jan 2006 15:04:05 -0700" | safeHTML }}</lastBuildDate>{{ end }}
    {{ with .OutputFormats.Get "RSS" }}
        {{ printf "<atom:link href=%q rel=\"self\" type=%q />" .Permalink .MediaType | safeHTML }}
    {{ end }}
    {{ range first 10 (where .Data.Pages "Section" "post")}}
    <item>
      <title>{{ .Title }}</title>
      <link>{{ .Permalink }}</link>
      <pubDate>{{ .Date.Format "Mon, 02 Jan 2006 15:04:05 -0700" | safeHTML }}</pubDate>
      {{ with .Site.Author.email }}<author>{{.}}{{ with $.Site.Author.name }} ({{.}}){{end}}</author>{{end}}
      <guid>{{ .Permalink }}</guid>
      <description>{{ .Content | html }}</description>
    </item>
    {{ end }}
  </channel>
</rss>
```

这样我们就可以在 rss 订阅链接时获取到全文输出啦。具体的原理可以见[原博客](https://xuanwo.io/2018/04/08/hugo-rss-output-all-content/)。

怀着对原博滔滔不绝的敬仰之情逛了一圈博客。发现原博竟然是人文院毕业的，却成为了一个如此优秀的程序员(我想马上原地自爆)。而且原博有优秀的探索精神，有良好的阅读文档的习惯。

## 一些改动
因为添加了 About 界面，而在测试过程中发现 rss 会输出 About 界面的内容，因此查询了一下使用方法，原文对文章的选择是：
 `{{ range first 10 .Data.Pages }}`，我在上面把它改成了：

```xml
{{ range first 10 (where .Data.Pages "Section" "post")}}
```
这样，在生成 rss 输出的时候就只会选择 post 中的最新10篇文章。




## 参考链接
1. [Hugo RSS 配置输出全文](https://xuanwo.io/2018/04/08/hugo-rss-output-all-content/)


