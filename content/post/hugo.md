---
title: "Hugo 的安装与 Github Pages 集成 Travis CI 部署"
date: 2019-05-06T22:03:59+08:00
lastmod: 2019-05-06T22:03:59+08:00
draft: true
keywords: []
description: ""
tags: []
categories: []
author: ""
---
## Hugo 的安装和使用
首先安装 Hugo

```shell
brew install hugo
```
然后用 Hugo 创建一个新的网页文件夹。
```shell
hugo new site mysite
```
<!--more-->
接着选择一个合适的主题，Hugo 的主题可以在[这里](https://themes.gohugo.io/)挑选。将主题文件下载到 `mysite/themes`文件夹中，可以直接在这个文件夹中`git clone`，记得修改文件夹的名称为主题名，不然默认是`hugo-theme-主题名`。以 even 主题为例：
```shell
git clone git@github.com:olOwOlo/hugo-theme-even.git even
```
在主题文件夹下会有一个 `exampleSite` 文件夹，其中的 `config.toml` 文件可以作为配置参考。我一般直接将这个文件覆盖 `mysite/config.toml` 再进行修改。

运行 hugo:
```
hugo server -t even -D
```
这里的 `-t` 用于选择 hugo 的主题，`-D` 代表在本地显示草稿文件。草稿文件就是在开头有`draft: true`的文件。

## 部署到 Github Pages


