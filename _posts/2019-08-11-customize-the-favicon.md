---
title: 自定义图标
author: cotes
date: 2019-08-11 00:34:00 +0800
categories: [博客, 教程]
tags: [网站图标]
---

[**Chirpy**](https://github.com/cotes2020/jekyll-theme-chirpy/) 的 [图标](https://www.favicon-generator.org/about/) 放在 `assets/img/favicons/`{: .filepath} 目录中。您可能希望将它们替换为自己的。以下部分将指导您创建和替换默认网站图标。

## 生成图标

准备大小为 512x512 或更大的方形图片（PNG、JPG 或 SVG），然后转到在线工具 [**Real Favicon Generator**](https://realfavicongenerator.net/) 并单击 <kbd>Select your Favicon image</kbd> 按钮上传您的图片文件。

在下一步中，网页将显示所有使用方案。您可以保留默认选项，滚动到页面底部，然后单击 <kbd>Generate your Favicons and HTML code</kbd> 按钮以生成网站图标。

## 下载和替换

下载生成的包，解压并删除以下两个文件：

- `browserconfig.xml`{: .filepath}
- `site.webmanifest`{: .filepath}

然后复制剩余的图片文件（`.PNG`{: .filepath} 和 `.ICO`{: .filepath}）以覆盖 Jekyll 站点 `assets/img/favicons/`{: .filepath} 目录中的原始文件。如果您的 Jekyll 网站还没有此目录，创建一个即可。

下表将帮助您了解对图标文件的更改：

| 文件                | 来自在线工具                        | 来自 Chirpy  |
|---------------------|:---------------------------------:|:-----------:|
| `*.PNG`             | ✓                                 | ✗           |
| `*.ICO`             | ✓                                 | ✗           |

>  ✓ 表示保留， ✗ 表示删除。
{: .prompt-info }

下次构建网站时，网站图标将替换为自定义的版本。
