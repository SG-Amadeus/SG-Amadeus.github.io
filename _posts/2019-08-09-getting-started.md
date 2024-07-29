---
title: 准备开始
author: cotes
date: 2019-08-09 20:55:00 +0800
categories: [博客, 教程]
tags: [入门]
pin: true
---

## 先决条件

按照 [Jekyll 文档](https://jekyllrb.com/docs/installation/) 中的说明完成 `Ruby` 、 `RubyGems` 、 `Jekyll` 和 `Bundler` 的安装。此外，还需要安装 [Git](https://git-scm.com/) 。

## 安装

### 创建新站点

有两种方法可以为此主题创建新的存储库：

- [**使用 Chirpy Starter**](#方式一-使用-chirpy-starter) - 易于升级，隔离不相关的项目文件，以便您可以专注于写作。
- [**Forking on GitHub**](#方式二-forking-on-github) - 方便自定义开发，但难以升级。除非您熟悉 Jekyll 并决定对该项目做出调整或贡献，否则不建议使用此方法。

#### 方式一： 使用 Chirpy Starter

从 [**Chirpy Starter**][use-starter] 创建一个新的存储库并将其命名为 `<GH_USERNAME>.github.io` ，其中 `GH_USERNAME` 表示您的 GitHub 用户名。

#### 方式二： Forking on GitHub

在 GitHub 上 [Fork **Chirpy**](https://github.com/cotes2020/jekyll-theme-chirpy/fork) 并将其重命名为 `<GH_USERNAME>.github.io` 。请注意，默认分支代码正在开发中。如果您希望网站稳定，请切换到最新的 [tag][latest-tag] 并开始编写。

然后执行：

```console
$ bash tools/init
```

> 如果您不想在 GitHub 页面上部署站点，请在上述命令末尾附加 `--no-gh` 选项。
{: .prompt-info }

上述命令将会：

1. 从存储库中删除 `_posts`{: .filepath} 目录。

2. 如果使用了 `--no-gh` 选项，则将删除 `.github`{: .filepath} 目录。否则，通过删除 `.github/workflows/pages-deploy.yml.hook`{: .filepath} 文件的 `.hook`{: .filepath} 扩展名来设置 GitHub Action 工作流，然后删除 `.github`{: .filepath} 文件夹中的其他文件和目录。

3. 从 `.gitignore`{: .filepath} 中删除 `Gemfile.lock` 条目 。

4. 创建新提交以自动保存更改。

### 安装依赖项

首次运行之前，定位到站点的根目录，并按如下所示安装依赖项：

```console
$ bundle
```

## 用法

### 配置

根据需要更新 `_config.yml`{: .filepath} 的变量。其中一些是典型的选项：

- `url`
- `avatar`
- `timezone`
- `lang`

### 自定义样式表

如果需要自定义样式表，请将主题的  `assets/css/style.scss`{: .filepath} 文件复制到你的 Jekyll 网站上的相同路径，然后在样式文件的末尾添加自定义样式。

从 [`v4.1.0`][chirpy-4.1.0] 开始，如果要覆盖 `_sass/addon/variables.scss`{: .filepath} 中定义的 SASS 变量，请创建一个新文件 `_sass/variables-hook.scss`{: .filepath} ，并为其中的目标变量分配新值。

### 自定义静态资源

在 `5.1.0` 版本中引入了静态资源配置。静态资源的 CDN 由 `_data/assets/cross_origin.yml`{: .filepath } 文件定义，您可以根据网站发布地区的网络情况进行替换。

另外，如果您想自托管静态资源，请参考 [_chirpy-static-assets_](https://github.com/cotes2020/chirpy-static-assets#readme) 。

### 运行本地服务器

您可能希望在发布之前预览网站内容，因此只需通过以下方式运行它：

```console
$ bundle exec jekyll s
```

或者使用以下命令在 Docker 上运行站点：

```console
$ docker run -it --rm \
    --volume="$PWD:/srv/jekyll" \
    -p 4000:4000 jekyll/jekyll \
    jekyll serve
```

一段时间后，本地服务将在 _<http://127.0.0.1:4000>_ 发布。

## 部署

在部署开始之前，请检查 `_config.yml`{: .filepath} 文件并确保 `url` 配置正确。此外，如果您更喜欢 [**项目站点**](https://help.github.com/en/github/working-with-github-pages/about-github-pages#types-of-github-pages-sites) 并且不使用自定义域名，或者您想使用 **GitHub Pages** 以外的 Web 服务器上的基本 URL 访问您的网站，请记住将 `baseurl` 更改为以斜杠开头的项目名称，例如 `/project-name` 。

现在，您可以选择以下方式之一来部署您的 Jekyll 站点。

### 使用 GitHub Actions 进行部署

确保您的 Jekyll 网站具有 `.github/workflows/pages-deploy.yml`{: .filepath} 文件。否则，请创建一个新文件并填写 [示例文件][workflow] 的内容， `on.push.branches` 的值应与存储库的默认分支名称相同。然后将您 GitHub 上的存储库重命名为 `<GH_USERNAME>.github.io` 。

此外，如果您已提交 `Gemfile.lock`{: .filepath} 文件到存储库并且您的本地计算机不是 Linux，请定位到站点的根目录并更新平台列表：

```console
$ bundle lock --add-platform x86_64-linux
```

现在发布您的 Jekyll 网站：

1. 到 GitHub 上浏览您的存储库。选择 _Settings_ 选项卡，然后单击左侧导航栏中的 _Pages_ 。然后，在 **Source** 部分（在 _Build and deployment_ 下面）中，从下拉菜单中选择 [**GitHub Actions**][pages-workflow-src] 。

2. 将任意提交推送到远程以触发 GitHub Actions 工作流。在存储库的 _Actions_ 选项卡中，应会看到正在运行中的工作流 _Build and Deploy_ 。构建完成并成功后，将自动部署站点。

3. 在 GitHub 指定的地址访问您的网站。

### 手动构建和部署

在自托管服务器上，您无法享受 **GitHub Actions** 的便利。因此，应在本地计算机上生成站点，然后将站点文件上传到服务器。

定位到源项目的根目录，并按如下所示构建站点：

```console
$ JEKYLL_ENV=production bundle exec jekyll b
```

或者在 Docker 上构建网站：

```console
$ docker run -it --rm \
    --env JEKYLL_ENV=production \
    --volume="$PWD:/srv/jekyll" \
    jekyll/jekyll \
    jekyll build
```

除非指定了输出路径，否则生成的站点文件将放置在项目根目录的 `_site`{: .filepath} 文件夹中。现在，您应该将这些文件上传到目标服务器。

## 升级

这取决于您如何使用主题：

- 如果您使用的是 gem 主题（在 `Gemfile`{: .filepath} 中会有 `gem "jekyll-theme-chirpy"` ），则编辑 `Gemfile`{: .filepath} 并更新 gem 主题的版本号，例如：

  ```diff
  - gem "jekyll-theme-chirpy", "~> 3.2", ">= 3.2.1"
  + gem "jekyll-theme-chirpy", "~> 3.3", ">= 3.3.0"
  ```
  {: .nolineno file="Gemfile" }

  然后执行以下命令：

  ```console
  $ bundle update jekyll-theme-chirpy
  ```

  随着版本升级，关键文件（有关详细信息，请参阅 [Startup Template][starter] ）和配置选项将会被更改。请参阅 [Upgrade Guide](https://github.com/cotes2020/jekyll-theme-chirpy/wiki/Upgrade-Guide) ，以使您的存储库的文件与最新版本的主题保持同步。

- 如果您是从源项目 fork 的（您的 `Gemfile`{: .filepath} 中会有 `gemspec` ），则将 [最新的上游 tag][latest-tag] 合并到您的 Jekyll 站点中以完成升级。该合并可能会与您的本地修改冲突。请耐心且仔细地解决这些冲突。

[starter]: https://github.com/cotes2020/chirpy-starter
[use-starter]: https://github.com/cotes2020/chirpy-starter/generate
[workflow]: https://github.com/cotes2020/jekyll-theme-chirpy/blob/master/.github/workflows/pages-deploy.yml.hook
[chirpy-4.1.0]: https://github.com/cotes2020/jekyll-theme-chirpy/releases/tag/v4.1.0
[pages-workflow-src]: https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site#publishing-with-a-custom-github-actions-workflow
[latest-tag]: https://github.com/cotes2020/jekyll-theme-chirpy/tags
