---
title: Hello from Jifeng...
---

# 使用Github Pages

官方有详细的文档可参考：[Github Pages基础](https://help.github.com/categories/github-pages-basics/)

## 简单步骤

- 若没有github账号，去注册一个；下面以`username`作为github账户名示例
- 若使用User Site，则新建一个Repo，命名为`username.github.io`；注意：必须叫这个名字
- 在新的Repo中创建一个`index.html`或`index.md`文件作为首页
- 访问`https://username.github.io/`

注：若使用Project Site(以`NewProject`名称的Repo为例)，可以在Project的Repo中新建一个gh-pages的分支(或者直接在主分支创建一个`/docs`文件夹来存放Github Pages的内容)，此分支的内容会在`https://username.github.io/NewProject/`展示出来。

## 定制页面

定制页面的官方文档：[定制Github Pages](https://help.github.com/categories/customizing-github-pages/)

建议使用Jekyll定制Github Pages，可以参考[Github的帮助页](https://help.github.com/articles/creating-a-github-pages-site-with-the-jekyll-theme-chooser/)。Jekyll可以使用一般的[页面](https://jekyllrb.com/docs/pages/)，也可以用来[写博客](https://jekyllrb.com/docs/posts/)，完整的项目文件夹结构可参见[Jekyll官方文档](https://jekyllrb.com/docs/structure/)。

- 到username.github.io的Repo
- 进入Settings菜单，找到`Github Pages`一栏
- 在`Theme Chooser`中选择一款主题样式即可

注：一般的Jekyll样式都有github的Repo的，比如[TimeMachine的Theme](https://github.com/pages-themes/time-machine)。

## 感谢

本博客的导航部分参考了[codinfox-lanyon](https://github.com/codinfox/codinfox-lanyon/)的代码，生成Tag和Category部分使用了[minddust的方法](http://www.minddust.com/post/alternative-tags-and-categories-on-github-pages/)，主题使用了[tactile主题](https://github.com/pages-themes/tactile/)，在此表示感谢！