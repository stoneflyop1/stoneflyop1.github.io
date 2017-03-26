---
layout: post
title: 使用Jekyll主题定制Github页面
description: 
category: programming
tags: [github, jekyll]
last_modified_at: 2017-03-26T00:00:00+00:00
---

作为开始，首先可以从Github默认的主题列表里选择一款主题；然后再根据自己的需要做一些定制：比如增加导航栏，文章的标签和类别等。


## 定制页面

- `include`指令: 引入其他文件内容到当前页(`include nav.html`)，需要引入的文件需要放到`_includes`文件夹中，一般为html文件。
- 修改样式表：在使用Jekyll主题后，一般可以添加一个`assets/css/style.scss`文件来覆盖现有的样式表，内容如下。
    ```css
    ---
    ---
    /* 导入Jekyll主题样式表 */
    @import "{{ site.theme }}";

    /* 用来覆盖的样式或新增样式 */
    span a.top-nav-item {
        width:100px;
        text-align: center;
    }
    ```
## 添加文章类别和标签

主要参考了[minddust的博客文章](http://www.minddust.com/post/alternative-tags-and-categories-on-github-pages/)，源代码参考[minddust.github.io](https://github.com/minddust/minddust.github.io/)。

使用collections生成类别和标签的链接，示例如下：

```yaml
# tags and categories
collections:
  my_categories:
    output: true
    permalink: /blog/category/:name/
  my_tags:
    output: true
    permalink: /blog/tag/:name/

...

# render layouts
defaults:
  -
    scope:
      path: ""
      type: my_categories
    values:
      layout: blog_by_category
  -
    scope:
      path: ""
      type: my_tags
    values:
      layout: blog_by_tag
```

## 注意的问题

- Jekyll中的页面为`page`，而不是`post`；分页插件中用的是`post`
- [使用sitemap插件可以增加文章的修改时间](https://zzz.buzz/2016/02/13/add-an-updated-field-to-your-jekyll-site/)，但要注意，[只有使用了`jekyll-last-modified-at`插件后才能自动生成`last_modified_at`的页面属性，否则需要手工在页面添加](https://github.com/jekyll/jekyll-sitemap#lastmod-tag)
- 数学公式可以使用[`MathJax`](http://docs.mathjax.org/en/latest/start.html#tex-and-latex-input)；示例：$ E=mc^2 $
- 数学公式中的符号可以使用[`tex`格式](http://www.colorado.edu/physics/phys4610/phys4610_sp15/PHYS4610_sp15/Home_files/LaTeXSymbols.pdf)，示例：$ x \in A $。为了正常显示所有的数学公式，markdown插件需要换成：kramdown