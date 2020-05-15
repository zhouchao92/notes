---
title: hexo实现置顶文章
tags:
  - hexo
date: 2020-04-11 11:18:24
---

#### hexo置顶文章

###### 安装插件

在博客根目录下运行：

```shell
npm uninstall hexo-generator-index --save	#卸载index渲染器
npm install hexo-generator-index-pin-top --save  #安装置顶插件
#安装淘宝的镜像可以使用 cnpm 代替 npm
```

###### 配置置顶标准

在`/themes/所使用的主题名/layout/post.ejs`文件最前面添加下列代码

```ejs
<% if (page.top) { %>
  <i class="fa fa-thumb-tack"></i>
  <font color=7D26CD>置顶</font>
  <span class="post-meta-divider">|</span>
<% } %>
...
```

###### 文章配置

在需要置顶的文章的**Front Matter**部分添加 `top: number`

number的数值越大优先级越高

```yml
---
title: 
date: YYYY-MM-DD HH:mm:ss
top: 2
---
```

###### 优先级配置

修改根目录配置文件`/_config.yml`,`top`值`-1`根据`top`值倒序

```yml
index_generator:
  path: ''
  per_page: 6	#每页显示的文章数
  order_by:
    top: -1		#按top倒序排列，number越大的优先显示
    date: -1	#按时间倒序排列，最新的优先显示
```

<!--more-->

###### 效果图

<img src="https://i.loli.net/2020/04/11/NjDQvAB6SOgKt4b.png" alt="hexo置顶效果图.png"  />

###### 参考文章

[Hexo yilia 配置--置顶文章部分](https://yangyang188.coding.me/archives/4392eea4/)