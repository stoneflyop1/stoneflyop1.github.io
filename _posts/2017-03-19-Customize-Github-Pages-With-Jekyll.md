---
title: 使用Jekyll主题定制Github页面
category: programming
tags: [github, jekyll]
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
