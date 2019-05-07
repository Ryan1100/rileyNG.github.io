---
title: "Hugo 的安装与 Github Pages 部署"
date: 2019-05-06T22:03:59+08:00
lastmod: 2019-05-06T22:03:59+08:00
draft: false
hidden: false
keywords: []
description: "Blog start up"
tags: ["hugo"]
categories: ["blog"]
author: ""
---
## Hugo 的安装和使用

### 安装
首先安装 Hugo

```shell
brew install hugo
```
然后用 Hugo 创建一个新的网页文件夹。
```shell
hugo new site mysite
```
<!--more-->
运行结束后可以看到 `mysite` 的文件结构：
```
├── archetypes
├── config.toml
├── content
├── data
├── layouts
├── static
└── themes
```
其中:
* themes: 主题文件夹
* static: 储存图片之类的静态文件，该目录下的文件会直接拷贝到/public，该文件夹的优先级高于主题下的/static文件夹
* content: 网站的所有内容
* config.toml: 配置文件
* archetypes: 存储生成`.md`时候的模板文件，优先级高于主题下的 `archetypes` 文件夹。

### 主题
接着选择一个合适的主题，Hugo 的主题可以在[这里](https://themes.gohugo.io/)挑选。将主题文件下载到 `mysite/themes`文件夹中，可以直接在这个文件夹中`git clone`，记得修改文件夹的名称为主题名，不然默认是`hugo-theme-主题名`。以 even 主题为例：
```shell
git clone git@github.com:olOwOlo/hugo-theme-even.git even
```
在主题文件夹下会有一个 `exampleSite` 文件夹，其中的 `config.toml` 文件可以作为配置参考。我一般直接将这个文件覆盖 `mysite/config.toml` 再进行修改。主要是修改网站的标题，中英文等等，还有就是进行一些配件的设置，比如评论功能的选择，Google Analytics 的设置等。

由于 even 主题默认没有 About 界面，所以我在配置文件中增加了：
```toml
[[menu.main]]
  name = "About"
  weight = 50
  identifier = "about"
  url = "/about/"
```
所以我们在 `content` 文件夹下新建一个名为 `about` 的目录，再新建一个 `index.md`，这里的内容就是 About 界面的内容了。


### 新增文章
新增文章不需要我们手动去新建一个`.md`文件，只需要在`mysite`目录下使用命令：
```shell
hugo new post/my-first-post.md
```
这样 hugo 会自动在 `content/post` 文件夹中新建一个 `my-first-post.md` 文件，文件内容按照 `archetypes` 里的模板文件来组织。

通常模板文件有以下内容：
```yaml
---
title: "{{ replace .TranslationBaseName "-" " " | title }}" #默认是文件名字作为标题
date: {{ .Date }} # 创建日期
lastmod: {{ .Date }} #最后修改日期，这个需要在配置中开启 enableGitInfo
draft: true # 是否为草稿
keywords: [] 
description: ""
tags: []
categories: []
author: ""
---
```
然后就可以愉快地开始用 Markdown 写内容啦。

如果开启了评论功能的话，可以在某些界面根据需求关闭，比如我在 About 界面中设置了以下选项：
```yaml
comment: false
toc: false
autoCollapseToc: false
postMetaInFooter: false
hiddenFromHomePage: false
```

### 运行
在本地预览运行 hugo:
```shell
hugo server -t even -D
```
这里的 `-t` 用于选择 hugo 的主题，`-D` 代表在本地显示草稿文件。草稿文件就是在开头有`draft: true`的文件。然后访问 `http://localhost:1313/` 就可以看到博客啦。

## 部署到 Github Pages

Github Pages 就不多说了，可以很方便地部署静态博客。在 Github 中先开一个 `{githubname}.github.io` 的 repo，然后把网站的静态文件放进去即可。

hugo 生成静态 `public` 文件：在 `mysite` 文件夹运行 `hugo` 命令即可得到一个 `public` 文件夹，将这个文件夹里的所有内容放到 Github 的相应 repo 里即可。**注意把 `config.tmol` 里的 baseURL 设置成你的 Github Pages 链接** (我最开始忘记了，然后很多资源就渲染不出来= =)
