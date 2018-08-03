---
title: "用hugo搭建个人博客"
date: 2018-03-14T10:14:08+08:00
draft: true
user: 李明燮
---

这几天研究了用hugo搭建个人博客。

简单的整理了一下。

**1.安装hugo**（windows 请查看官网介绍 https://gohugo.io/getting-started/installing/）

```
$ brew install hugo
```
之后检查版本
```
$ hugo version
Hugo Static Site Generator v0.37.1 darwin/amd64 BuildDate:
```

**2.创建hugo 项目**

```
$ hugo new site my-blog
Congratulations! Your new Hugo site is created in /Users/limingxie/Projects/my-blog.

Just a few more steps and you're ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/, or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.
```

**3.添加模板**

```
$ cd my-blog
$ git init
$ git submodule add https://github.com/keichi/vienna.git themes/vienna
```

**4.添加内容**（content目录下执行。例：/Users/limingxie/Projects/my-blog/content）

```
$ hugo new xxx/my-first-blog.md
```
编辑：my-first-blog.md

**5.运行调试**

```
$ hugo server -D

                   | EN
+------------------+----+
  Pages            | 16
  Paginator pages  |  0
  Non-page files   |  0
  Static files     | 14
  Processed images |  0
  Aliases          |  1
  Sitemaps         |  1
  Cleaned          |  0

Total in 26 ms
Watching for changes in /Users/limingxie/Projects/my-blog/{content,data,layouts,static,themes}
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender
Web Server is available at //localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```
访问[http://localhost:1313/](http://localhost:1313/)调试~

**6.Theme的设定**

打开config.toml
```
baseURL = "https://limingxie.github.io/" //这baseURL是部署后的访问地址。
languageCode = "en-us"
title = "My New Hugo Site"
theme = "vienna" // 你使用的theme 名称
```
查看[vienna Theme](https://github.com/keichi/vienna)说明的话还可以做很多设置。

**7.部署到GitHub**

1) 首先在GitHub上创建一个Repository，命名为：limingxie.github.io (这要和你的baseURL一致。)
2）在/Users/limingxie/Projects/my-blog目录下执行以下命令
```
$ hugo --theme=vienna --buildDrafts --baseUrl="https://limingxie.github.io/"

                   | EN
+------------------+----+
  Pages            | 16
  Paginator pages  |  0
  Non-page files   |  0
  Static files     | 14
  Processed images |  0
  Aliases          |  1
  Sitemaps         |  1
  Cleaned          |  0

Total in 28 ms
```
 --buildDrafts 不加会生成无内容的网站
 --baseUrl要和config.toml的地址一致。

 顺利的话会创建public文件（里面的内容就是你要上传的静态网站的文件。）

 ```
$ cd public
$ git init
$ git remote add origin https://github.com/limingxie/limingxie.github.io.git
$ git add -A
$ git commit -m "first commit"
$ git push -u origin master
 ```

### 浏览器里访问：https://limingxie.github.io/ 就可以访问你的博客了


----------------------------------------------
欢迎大家的意见和交流

`email: li_mingxie@163.com`