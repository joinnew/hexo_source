---
title: hexo-setting 配置
date: 2019-10-11 12:23:38
tags:
- hexo配置
categories: 工具使用配置
---
这里记录一下博客hexo的使用和配置，方便后期回顾

<!-- more --->

### 创建一个博客

#### 安装hexo-cli
```bash
    npm install -g hexo-cli
```
#### 创建hexo文件夹
```bash
    hexo init 名字
```
#### 安装依赖包
切换到hexo所在目录下
```bash
    npm install
```
### hexo常见的命令
```bash
    hexo new ‘postName’ # 新建文章，一篇篇的博客文章 对应缩写是hexo n
    hexo new page 'pageName' # 新建页面,像是‘主页’这种的，可以包含多个文章
    hexo generate # 生成静态页面至public目录，需要先执行这个生成 对应缩写是hexo g
    hexo server # 开启预览访问端口（默认端4000) 对应缩写是hexo s
    hexo deplay # 部署到GitHub 对应缩写是hexo d
    hexo help # 查看帮助
```

<font color=red>部署的时候可能会提示git 不存在什么的，那就需要安装**hexo-deployer-git** 依赖，</font>
```bash 
    npm install --save hexo-deployer-git
```

### hexo常见的组合命令
```bash
    hexo s -g # 生成并本地预览
    hexo d -g # 生成并部署到github上
```

### 首先需要配置的
- 想要上传到github需要配置(针对ssh key已经配置好的)最外面的_config.yml文件
```bash
    deploy: 
        type: git
        repository: 自己的地址，ssh的
        branch: master
```

### 修改博客主题
我这里的都是以某一个为主题效果的，当然每个人喜欢的可能不一样，所以索性把如何修改主题也放到这里
#### 可选择的博客主题地址 
[博客主题地址](https://hexo.io/themes/)

#### 如何更换
- 选择一个喜欢的主题，如图所示，点击下方的名字，进入github仓库地址
<img src='http://pytyayr4p.bkt.clouddn.com/QQ20191011-132123@2x.png' width=500/>

- 复制他的下载地址，并且更换配置主题
```bash
    git clone xxx themes/主题名 # 如 git clone https://github.com/litten/hexo-theme-yilia.git themes/yilia

    下好之后，需要在最外围的_config.yml中修改theme的值 # 如 theme: yilia
```

### 修改博客配置，记录一下我用到的配置
我这里涉及到作者名、用户头像、所有文章按钮生效、加上分类操作等

#### 修改展示的作者名
- 找到根目录的_config.xml文件，里面的author就是配置，修改成自己喜欢的就可以了。

#### 主页上文章的内容只是想展示一部分
- yilia的默认主页中，文章是展示所有内容。有时候只是想展示一部分。可以在文章的内容区域加上<!-- more -->,在这之前的就会出现，在这之后的就会隐藏。同时将themes里的yilia的_config.yml进行设置
```bash 
    excerpt_link: more # 设置展示更多的按钮文字内容
    show_all_link: false # 将文章右下角的常驻按钮去掉
```

#### 设置头像
- 在yilia的source中建立assets，将头像图片放在这里，_config.yml中设置
```bash 
    avatar: /assets/xxx # 这里默认取source中的内容，所以地址取相对的，且是以source为选取的目标文件夹
```

#### 智能菜单设置
- yilia中默认有三个（所有文章、友链、关于我），想要去掉其中一个，可以设置，将对应的设置为false就会隐藏了.
```bash
    smart_menu:
        innerArchive: "所有文章"
        friends: false
        aboutme: "关于我"
```

#### 所有文章按钮想要生效
其实点击所有文章的时候就会有一些提示内容，都可以看到的。这里就记录怎么处理
- 安装依赖
```bash 
    npm i hexo-generator-json-content --save 
```

- 在根目录下的_config.yml设置,默认是没有这些的，需要自己额外添加
```bash 
    jsonContent: 
        meta: false
        pages: false
        posts:
            title: true
            date: true
            path: true
            text: false
            raw: false
            content: false
            slug: false
            updated: false
            comments: false
            link: false
            permalink: false
            excerpt: false
            categories: false
            tags: true
```

- 需要注意的是：确保node的版本。如果版本低于6.2的话，可以通过安装n模块来管理node不同版本，这样就不一定需要升级了。
```bash 
    sudo npm install -g n # 全局安装n
    sudo n v10.12.0 # 下载对应的node版本
    sudo n stable # 升级node到最新稳定版
    sudo n latest # 升级node到最新版
    sudo n 10.12.0 # 切换到这个版本上来
```

#### 修改关于我的内容
- 在/themes/yilia/_config.yml中修改**aboutme**的内容就可以了。

#### yilia主题下增加tags标签部分
- 在对应的页面md文件中，头部加上
```bash 
    data: xxx # 坐标，用于提示tags的位置
    tags: 
        - 前端
        - 博客配置
```

#### yilia主题下增加categories分类模块
- 在对应的页面也就是md文件中，加上
```bash
    tags: xxx # 坐标，用于提示位置
    categories: 工具使用配置
```

- 在左边的导航栏部分加上分类的页面
```bash 
    hexo new page "categories" # 会建立/source/categories/index.md文件

    # 以下是文件的内容
    title: categories
    date: xxx # 可以随意设置成自己想要的时间
    type: categories
    layout: categories
    comments: false
```

- 给分类页面设置界面效果
在/themes/yilia/layout中添加categories.ejs - 主要是跟categories/index.md的layout一致,复制别人的效果

```js
  <article class="article article-type-post show">
    <header class="article-header">
    <h1 class="article-title" itemprop="name">
      <%= page.title %>
    </h1>
    </header>
  
    <% if (site.categories.length){ %>
    <div class="category-all-page article-type-post show">
      <h3>共计&nbsp;<%= site.categories.length %>&nbsp;个分类</h3>
      <ul class="category-list">
      <% site.categories.sort('name').each(function(item){ %>
        <% if(item.posts.length){ %>
          <li class="category-list-item">
            <a href="<%- config.root %><%- item.path %>" title="<%= item.name %>"><%= item.name %><sup>[<%= item.posts.length %>]</sup></a>
          </li>
        <% } %>
      <% }); %>
      </ul>
    </div>
    <% } %>
  </article>
```

设置样式 /themes/yilia/source-src/css/categories.scss文件

<font color=red>注意：要在main.css中引入categories.scss文件</font>

```css
    .category-all-page {
    margin: 30px 40px 30px 40px;
    position: relative;
    min-height: 70vh;
  
    h3{
      margin: 20px 0;
    }
    .category-all-title { text-align: center; }
  
    .category-all { margin-top: 20px; }
  
    .category-list {
      margin: 0;
      padding: 0;
      list-style: none;
    }
  
    .category-list-item { margin: 10px 10px; }
  
    .category-list-count {
      color: $grey;
      &:before {
        display: inline;
        content: " ("
      }
      &:after {
        display: inline;
        content: ") "
      }
    }
  
    .category-list-child { padding-left: 10px; }
  }
```

### 利用七牛云保存需要的图片

- 注册一个七牛云账号

- 选择管理控制台
<br/>
<img src='http://pytyayr4p.bkt.clouddn.com/QQ20191011-123410@2x.png' width=400>

- 内容管理 - 上传文件 - 选择复制外链 - 在md文件中引入
```html
<img src='http://pytyayr4p.bkt.clouddn.com/QQ20191011-123410@2x.png' width=400>
```

<font color=red>因为hexo中的markdown还不支持对图片裁剪大小，所以通过标签来引入，标准版的markdown支持
```bash
![图片](url = 400x) 宽x高 也可以只设置其中一个属性
```
</font>

### 如何把源代码上传到仓库中
当我们通过hexo g -d部署到仓库上时，并没有把配置文件、md文件之类的上传，而只是生成一些博客需要的静态的页面进行上传

- 可以通过创建新的仓库专门用于保存源码(主要是配置的几个文件)，也可以在当前仓库创建新的分支用于保存

#### 本地创建新分支
```bash
    git checkout -b feature/hexo-source
```
#### 提交代码
```bash
    git add .
    git commit -m '源码提交'
    git push origin feature/hexo-source
```