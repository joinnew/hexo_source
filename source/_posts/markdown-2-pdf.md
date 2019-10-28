---
title: markdown-2-pdf
date: 2019-08-12 20:28:39
tags: Markdown PDF工具
categories: 工具使用配置
---
简历想要符合自己的想法来写，使用模版也不好操作。索性直接用markdown的形式来写好了，够简约。不过其实是借用了vscode的插件，嘿嘿。其有自己的默认格式，这里还记录了如何修改这些默认格式，比如：去掉右上角的时间展示

<!-- more --->
### 如何使用
- VSCode安装插件 Markdown PDF
- 安装完成了，选择一个md文件，右键即可选择导出类型，ps: 导出的文件默认就在md文件所在文件夹中

### 如果转换失败，右下角提示ERROR: Failed to download Chromium!
- 这是因为Chromium没有下载成功导致的，网站被墙导致下载失败。我们可以配置chrome浏览器的地址来解决这个问题

- 找到chrome的运行文件（windows中是以.exe结尾的，而mac中可以把应用程序拖到终端中进行查看地址）

- 选择VSCode的右上角Code选项-首选项-设置-找到markdown-pdf（任意一个，点击 下边的在settings.json中配置Edit in settings.json）
```bash
    "markdown-pdf.executablePath": "\/Applications\/Google Chrome.app\/Contents\/MacOS\/Google Chrome"
```

### 修改默认格式
- 找到Markdown PDF的setting.json配置
```bash
    "markdown-pdf.headerTemplate": "<div></div>" # 设置头部的展示内容
    "markdown-pdf.footerTemplate": "<div></div>" # 设置尾部的展示内容
```