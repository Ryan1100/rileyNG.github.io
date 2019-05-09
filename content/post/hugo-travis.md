---
title: "Hugo 博客使用 Travis CI 部署"
date: 2019-05-07T10:38:37+08:00
lastmod: 2019-05-07T10:38:37+08:00
draft: false
hidden: false
keywords: []
description: "Travis with hugo"
tags: ["travis","hugo"]
categories: ["blog"]
author: ""
---

由于 Hugo 没有像 Hexo 或者 Jekyll 那样和 Github Pages 集成，所以每次都需要手动生成 `public` 文件夹然后再部署到 Github Pages 的仓库。为了减少这种重复劳动，我开始寻找可以自动部署的方案，查到有两种：

1. 写一个脚本，每次发布文章时可以手动运行脚本来生成静态文件并部署
2. 使用 Travis CI 来进行自动部署

由于没有使用过 Travis CI ，想要尝试一下，而且写脚本来完成手动工作看起来还是没有 Travis CI 那么优雅，最终我选择了 Travis CI 作为解决方案。

<!--more-->

## Travis CI 配置
Travis CI提供持续集成服务（Continuous Integration，简称 CI）。它可以绑定 Github 上面的项目，只要有新的代码，就会自动抓取。然后，提供一个运行环境，执行测试，完成构建，还能部署到服务器。[这里](https://travis-ci.org/)是 Travis CI 的官网，用 Github 帐号注册登录。

为了给 Travis CI 接触 repo 的权限，首先去 Github -> Developer settings -> personal access tokens 中生成一个 token。因为是公开的，所以我们选择 `public_repo`，`repo:status`，`repo_deployment` 这三项权限即可。

**生成的 token 记得及时记录下来，因为之后就看不到了，而且千万不要泄漏**

在 Travis 的网站可以看到你的 repo，然后再选择 `{githubname}.github.io` 这个仓库，选择 `settings`，找到 `Environment Variables`，输入变量名：`GITHUB_TOKEN`和变量内容：`刚刚获取到的 token`。

然后我们的 Travis 设置就完成了。

## Github 仓库设置
因为不想再开太多的博客相关的 repo，所以我选择在 `{githubname}.github.io` 下面新建一个 `hugo` 分支：
```git
git checkout -b hugo
```
在这个分支中我们用来存储 Hugo 博客的代码，也就是上文中提到的 `mysite` 文件夹中的所有内容，所以我就直接复制了一下。

### .travis.yml 

Travis 要求项目的根目录下面，必须有一个 `.travis.yml` 文件。这是配置文件，指定了 Travis 的行为。该文件必须保存在 Github 仓库里面，一旦代码仓库有新的 Commit，Travis 就会去找这个文件，执行里面的命令。我的配置文件如下：

```yaml
language: go

go:
  - "1.8"  # 指定Golang 1.8

# Specify which branches to build using a safelist
# 分支白名单限制: 只有hugo分支的提交才会触发构建
branches:
  only:
    - hugo 

# install:
# # 安装最新的hugo
#   - go get github.com/spf13/hugo 
install:
    - uname -a
    - wget https://github.com/gohugoio/hugo/releases/download/v0.55.4/hugo_0.55.4_Linux-64bit.deb
    - sudo dpkg -i hugo*.deb
    - hugo version
    - ls
    - pwd

script:
# 运行hugo命令
  - hugo

deploy:
  provider: pages # 重要，指定这是一份github pages的部署配置
  skip-cleanup: true # 重要，不能省略
  local-dir: public # 静态站点文件所在目录
  target-branch: master # 要将静态站点文件发布到哪个分支
  github-token: $GITHUB_TOKEN # 重要，$GITHUB_TOKEN是变量，需要在GitHub上申请、再到配置到Travis
  #fqdn: blog.yuantops.com # 如果是自定义域名，此处要填
  keep-history: true # 是否保持target-branch分支的提交记录
  on:
    branch: hugo # 博客源码的分支
```
本来想要用 `go get github.com/spf13/hugo` 来获取最新的 hugo 版本，但是在 Travis 中不能这样获取，所以只能手动设置 hugo 的下载链接了，如果要修改使用的 hugo 版本的话，也需要以后手动修改 `.travis.yml` 文件中的下载链接。

配置完成之后，只要我们在 hugo 分支提交了 commit ，Travis 就能帮我们自动发布到博客上。如果要正式发布文章，记得设置 `draft: false`。

## 参考链接
1. [Blog自动部署实践: Hugo + Travis CI -> GitHub Pages](https://blog.yuantops.com/tech/hugo-travis-ci-auto-deploy-to-gh-pages/)
2. [使用 Travis CI 自动部署 Hugo 博客](https://mogeko.me/2018/028/)
3. [利用Travis CI和Hugo將Blog自動部署到Github Pages](https://axdlog.com/zh/2018/using-hugo-and-travis-ci-to-deploy-blog-to-github-pages-automatically/)