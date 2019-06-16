---
layout: post

title: 使用VSCode创建F#项目

description: 使用VSCode创建F#项目

category: programming

tags: [netcore, fsharp]
---

## 准备工作

1. 安装[.net](https://dotnet.microsoft.com/)
2. 安装[VSCode](https://code.visualstudio.com/)，并把`Code`执行路径加入到`PATH`环境变量中
3. 安装VSCode扩展：`Ionide-fsharp`；若使用[Fake](https://fake.build/)作为构建工具，则需要还需要安装`Ionide-FAKE`

## 创建项目

创建步骤如下：

1. 从终端(Windows下可以用cmd,powershell;linux或mac用Shell)切换到要创建项目的目录，键入：`code .`：VSCode会打开当前文件夹
2. 打开VSCode的命令窗口(Windows下使用`Ctrl+Shift+P`、Mac下使用`Shift+Command+P`)
3. 在VSCode的命令窗口输入：`F#:`，选择`F#: New Project`命令
4. 接着会提示项目创建所在的目录（当前目录的下级目录，留空则为当前目录）
5. 输入项目名称，项目创建结束

项目创建完成后，项目默认的是目标框架是`net461`，在mac或linux环境，需要改成`netstandard2.0`或`netcoreapp2.x`(如：netcoreapp2.2)。

若使用了`Paket`作为构建工具，则还需要把`paket.dependencies`文件中的`famework: >= net461`一行删除；删除后，在VSCode命令行执行`paket install`命令还原包(package)。

详细步骤和如何使用VSCode进行F#开发可以参考atlemann的博文[fsharp solutions from scratch](https://atlemann.github.io/fsharp/2018/02/28/fsharp-solutions-from-scratch.html)。
