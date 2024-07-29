---
title: 撰写新帖子
author: cotes
date: 2019-08-08 14:10:00 +0800
categories: [博客, 教程]
tags: [写作]
render_with_liquid: false
---

本教程将指导您如何在 _Chirpy_ 模板中撰写帖子，即使您以前使用过 Jekyll 也值得一读，因为许多功能都需要设置特定的变量。

## 命名和路径

创建一个名为 `YYYY-MM-DD-TITLE.EXTENSION`{: .filepath} 的新文件并将其放在根目录的 `_posts`{: .filepath} 中。请注意， `EXTENSION`{: .filepath} 必须是 `md`{: .filepath} 和 `markdown`{: .filepath} 之一。如果您想节省创建文件的时间，请考虑使用插件 [`Jekyll-Compose`](https://github.com/jekyll/jekyll-compose) 来完成此操作。

## 头信息

基本上，您需要在帖子顶部填写以下[头信息](https://jekyllrb.com/docs/front-matter/)：

```yaml
---
title: TITLE
date: YYYY-MM-DD HH:MM:SS +/-TTTT
categories: [TOP_CATEGORIE, SUB_CATEGORIE]
tags: [TAG]     # TAG 名称应始终小写
---
```

> 帖子的 _layout_ 已默认设置为 `post` ，因此无需在头信息块中添加变量 _layout_。
{: .prompt-tip }

### 时区

为了准确记录帖子的发布日期，您不仅应该设置 `_config.yml`{: .filepath} 的 `timezone` ，而且还应该在帖子头信息块的变量中提供时区。格式： `+/-TTTT` ， 例如 `+0800`。

### 分类和标签

每个帖子的 `categories` 被设计为最多包含两个元素，并且 `tags` 的元素数量可以是零到无穷大。例如：

```yaml
---
categories: [动物, 昆虫]
tags: [蜜蜂]
---
```

### 作者信息

帖子的作者信息通常不需要在 _头信息_ 中填写，默认情况下会从配置文件中 `social.name` 变量和 `social.links` 的第一个条目中获取。但您也可以按如下方式覆盖它：

在 `_data/authors.yml` 中添加作者信息（如果您的网站没有此文件，请立即创建一个）。

```yaml
<author_id>:
  name: <full name>
  twitter: <twitter_of_author>
  url: <homepage_of_author>
```
{: file="_data/authors.yml" }


然后用 `author` 指定单个条目或用 `authors` 指定多个条目：

```yaml
---
author: <author_id>                     # 针对单个作者
# or
authors: [<author1_id>, <author2_id>]   # 针对多个作者
---
```


话虽如此， `author` 也可以识别多个条目。

> 从 `_data/authors.yml`{: .filepath } 文件中读取作者信息的好处是页面将具有 `twitter:creator` 元标记，这丰富了 [Twitter Cards](https://developer.twitter.com/en/docs/twitter-for-websites/cards/guides/getting-started#card-and-content-attribution) ，并且有利于 SEO 。
{: .prompt-info }

## 目录

默认情况下，目录 （TOC） 显示在帖子的右侧面板上。如果要全局关闭它，请在 `_config.yml`{: .filepath} 文件中将 `toc` 变量的值设置为 `false` 。如果要关闭特定帖子的目录，请将以下内容添加到帖子的 [头信息](https://jekyllrb.com/docs/front-matter/) 内容中：

```yaml
---
toc: false
---
```

## 评论

评论的全局切换由 `_config.yml`{: .filepath} 文件中的 `comments.active` 变量定义。为此变量选择评论系统后，系统将为所有帖子启用评论。

如果要关闭特定帖子的评论，请将以下内容添加到帖子的 **头信息** 中：

```yaml
---
comments: false
---
```

## 数学

出于网站性能原因，默认情况下不会加载数学功能。但它可以通过以下方式启用：

```yaml
---
math: true
---
```

## Mermaid

[**Mermaid**](https://github.com/mermaid-js/mermaid) 是一个很棒的图表生成工具。要在帖子中启用它，请将以下内容添加到 YAML 块：

```yaml
---
mermaid: true
---
```

然后你可以像其他 markdown 语言一样使用它：用 ```` ```mermaid ```` 和 ```` ``` ```` 包裹图形代码。

## 图片

### 标题

将斜体添加到图片的下一行，然后它将成为标题并出现在图片的底部：

```markdown
![图片描述](/path/to/image)
_图片标题_
```
{: .nolineno}

### 大小

为了防止页面内容布局在加载图片时移动，我们应该设置每个图片的宽度和高度：

```markdown
![Desktop View](/assets/img/sample/mockup.png){: width="700" height="400" }
```
{: .nolineno}

从 _Chirpy v5.0.0_ 开始，支持缩写（`height` → `h`， `width` → `w`）。以下示例具有与上述相同的效果：

```markdown
![Desktop View](/assets/img/sample/mockup.png){: w="700" h="400" }
```
{: .nolineno}

### 位置

默认情况下，图片居中，但您可以使用 `normal` 、 `left` 和 `right` 类之中的一个指定位置。

> 指定位置后，不应添加图片标题。
{: .prompt-warning }

- **正常位置**

  在下面的示例中图片将会左对齐：

  ```markdown
  ![Desktop View](/assets/img/sample/mockup.png){: .normal }
  ```
  {: .nolineno}

- **向左浮动**

  ```markdown
  ![Desktop View](/assets/img/sample/mockup.png){: .left }
  ```
  {: .nolineno}

- **向右浮动**

  ```markdown
  ![Desktop View](/assets/img/sample/mockup.png){: .right }
  ```
  {: .nolineno}

### 深色/浅色模式

您可以使图片在深色/浅色模式下遵循主题首选项。这要求您准备两个图片，一个用于深色模式，一个用于浅色模式，然后为它们分配一个特定的类（ `dark` 或 `light` ）：

```markdown
![Light mode only](/path/to/light-mode.png){: .light }
![Dark mode only](/path/to/dark-mode.png){: .dark }
```

### 阴影

程序窗口的屏幕截图可以考虑显示阴影效果：

```markdown
![Desktop View](/assets/img/sample/mockup.png){: .shadow }
```
{: .nolineno}

### CDN URL

如果将图片托管在 CDN 上，可以通过指定 `_config.yml`{: .filepath} 文件中的 `img_cdn` 变量来节省重复编写 CDN URL 的时间：

```yaml
img_cdn: https://cdn.com
```
{: file='_config.yml' .nolineno}

指定 `img_cdn` 后，CDN URL 将被添加到以 `/` 开头的所有图片（网站头像和帖子的图片）的路径中。

例如，使用图片时：

```markdown
![The flower](/path/to/flower.png)
```
{: .nolineno}

解析结果会自动在图片路径前添加 `https://cdn.com` CDN 前缀：

```html
<img src="https://cdn.com/path/to/flower.png" alt="The flower">
```
{: .nolineno }

### 图片路径

当帖子包含许多图片时，重复定义图片的路径将是一项耗时的任务。为了解决这个问题，我们可以在帖子的 YAML 块中定义此路径：

```yml
---
img_path: /img/path/
---
```

然后，Markdown 的图片源可以直接写文件名：

```md
![The flower](flower.png)
```
{: .nolineno }

输出将会是：

```html
<img src="/img/path/flower.png" alt="The flower">
```
{: .nolineno }

### 预览图片

如果要在文章顶部添加图片，请提供分辨率为 `1200 x 630` 的图片。请注意，如果图片宽高比不符合 `1.91 : 1` ，图片将被缩放和裁剪。

了解这些先决条件后，您可以开始设置图片的属性：

```yaml
---
image:
  path: /path/to/image
  alt: image alternative text
---
```

注意， [`img_path`](#图片路径) 也可以传递给预览图片，也就是说，在设置好后， `path` 属性只需要图片文件名。

为了简单使用，您也可以只用 `image` 定义路径。

```yml
---
image: /path/to/image
---
```

### LQIP

对于预览图片：

```yaml
---
image:
  lqip: /path/to/lqip-file # or base64 URI
---
```

> 您可以在帖子 [_文本和排版_](../text-and-typography/) 的预览图片中观察 LQIP。


对于普通图片：

```markdown
![Image description](/path/to/image){: lqip="/path/to/lqip-file" }
```
{: .nolineno }

## 置顶帖子

您可以将一个或多个帖子置顶到首页，置顶的帖子会根据其发布日期以相反的顺序排序。启用方式：

```yaml
---
pin: true
---
```

## 提示

有几种类型的提示： `tip` 、 `info` 、 `warning` 和 `danger` 。可以通过将 `prompt-{type}` 类添加到块引用来生成它们。例如，定义 `info` 类型的提示，如下所示：

```md
> 提示的使用演示。
{: .prompt-info }
```
{: .nolineno }

## 语法

### 内联代码

```md
`inline code part`
```
{: .nolineno }

### 高亮文件路径

```md
`/path/to/a/file.extend`{: .filepath}
```
{: .nolineno }

### 代码块

Markdown 的 ```` ``` ```` 符号可以轻松创建代码块，如下所示：

````md
```
This is a plaintext code snippet.
```
````

#### 指定语言

使用 ```` ```{language} ```` 您将获得一个带有语法高亮的代码块：

````markdown
```yaml
key: value
```
````

> Jekyll 标签 `{% highlight %}` 与此主题不兼容。
{: .prompt-danger }

#### 行号

默认情况下，除 `plaintext` 、 `console` 和 `terminal` 之外的所有语言都将显示行号。如果要隐藏代码块的行号，请将 `nolineno` 类添加到其中：

````markdown
```shell
echo 'No more line numbers!'
```
{: .nolineno }
````

#### 指定文件名

您可能已经注意到，代码语言将显示在代码块的顶部。如果要将其替换为文件名，可以添加 `file` 属性来实现此目的：

````markdown
```shell
# content
```
{: file="path/to/file" }
````

#### Liquid 代码

如果要显示 **Liquid** 代码片段，请在 liquid 代码两边加上 `{% raw %}` 和 `{% endraw %}` ：

````markdown
{% raw %}
```liquid
{% if product.title contains 'Pack' %}
  This product's title contains the word Pack.
{% endif %}
```
{% endraw %}
````

或添加 `render_with_liquid: false` （需要 Jekyll 4.0 或更高版本）到帖子的 YAML 块中。

## 视频

您可以使用以下语法嵌入视频：

```liquid
{% include embed/{Platform}.html id='{ID}' %}
```
其中 `Platform` 是平台名称的小写， `ID` 是视频 ID。

下表显示了如何在给定的视频 URL 中获取我们需要的两个参数，您还可以了解当前支持的视频平台。

| 视频 URL                                                                                           | 平台       | ID            |
|----------------------------------------------------------------------------------------------------|-----------|:--------------|
| [https://www.**youtube**.com/watch?v=**H-B46URT4mg**](https://www.youtube.com/watch?v=H-B46URT4mg) | `youtube` | `H-B46URT4mg` |
| [https://www.**twitch**.tv/videos/**1634779211**](https://www.twitch.tv/videos/1634779211)         | `twitch`  | `1634779211`  |



## 了解更多信息

有关 Jekyll 帖子的更多信息，请访问 [Jekyll 文档：帖子](https://jekyllrb.com/docs/posts/) 。
