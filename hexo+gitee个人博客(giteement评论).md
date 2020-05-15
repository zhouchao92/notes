---
title: hexo上传至gitee以及添加gitee评论功能
tags:
  - hexo
  - gitee
  - giteement
abbrlink: 6dfbadcb
date: 2020-04-09 17:05:22
---

#### 使用gitee(码云)作为博客地址

在完成github和hexo搭建好个人博客系统后，再将博客部署到在gitee上

###### 创建gitee仓库

[新建仓库](https://gitee.com/projects/new)

为了使用`用户名.gitee.io`作为博客的地址，仓库名需要直接填写成gitee用户名，这一点是不同与github

原因：gitee pages在生成页面时会默认地添加后缀`.gitee.io`

- 仓库名：gitee用户名
- 开源：公开(可以作为giteement存放评论的仓库，另外新建一个也是可以的)

###### 配置

修改博客根目录下的`_config.ym`文件

```yaml
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: https://用户名.gitee.io/

# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@gitee.com:用户名/用户名.git
  branch: master
```

###### 部署到gitee仓库

在博客根目录下运行`git bash`或命令行窗口

```shell
//清除数据
hexo clean
//重新渲染
hexo g
//本地local host预览
hexo s
//部署到gitee仓库
hexo d
```

###### gitee使用gitee pages

在gitee的仓库下依次点击服务——>Gitee Pages——>启动

提示：已开启 Gitee Pages 服务，网站地址：`https://用户名.gitee.io`

即可访问

注意：使用gitee作为博客网站时，在每次推送到仓库后都要手动更新，即：服务——>Gitee Pages——>更新	网站才会更新

<!--more-->

#### giteement评论功能

###### 生成gitee第三方应用

[创建第三方应用](https://gitee.com/oauth/applications/new)

- 应用名称：自定义
- 应用描述：自定义
- 应用主页：https://用户名.gitee.io/
- 应用回调：https://用户名.gitee.io/
- 权限：issues	notes

在创建成功后会生成**Client ID** 	**Client Secret**

###### 添加相关文件

在`themes/yilia/layout`路径`index.ejs`文件中添加以下代码

```ejs
<script src="https://giteement.oss-cn-beijing.aliyuncs.com/oauthcallback.browser.js"></script><script>
    Oauthcallback();
</script>
```

在`themes/yilia/layout/_partial`路径下`article.ejs`文件中添加以下代码

```ejs
  <% if (theme.gitment_owner && theme.gitment_repo &&theme.gitment_oauth && theme.gitment_oauth.client_id && theme.gitment_oauth.client_secret){ %>
  <%- partial('post/gitment', {
      key: post.slug,
      title: post.title,
      url: config.url+url_for(post.path)
    }) %>
  <% } %>
<!--在此处以下代码-->
<!--giteement评论功能-->
<% if (theme.giteement.enable){ %>
<div id="giteementDiv"></div>
<link rel="stylesheet" href="https://giteement.oss-cn-beijing.aliyuncs.com/default.css">
<script src="https://giteement.oss-cn-beijing.aliyuncs.com/giteement.browser.js"></script>
<script>
var giteement = new Giteement({
  id: '<%- post.date.format('YYYYMMDDHHmmss') %>',
  owner: '<%- theme.giteement.giteeID %>',
  repo: '<%- theme.giteement.repo %>',
  backcall_uri: '<%- theme.giteement.redirect_uri %>',
  oauth_uri: '<%- theme.giteement.oauth_uri %>',
  oauth: {
    client_id: '<%- theme.giteement.ClientID %>',
    client_secret: '<%- theme.giteement.ClientSecret %>'
  },
})
giteement.render('giteementDiv')
</script>
```

###### 配置

修改themes/yilia目录下的`themes/yilia/_config.ym`文件

```yml
giteement:
  enable: true# 是否启用码云评论系统
  # 是否使用官方js(false可以提升访问速度)
  remote: false
  redirect_uri: ''   # 应用回调地址(请和配置的第三方应用保持一致)
  oauth_uri: https://cors-anywhere.herokuapp.com/https://gitee.com/oauth/token
  giteeID:   # 你的码云账号英文名
  # 存储评论的 repo name(需要在码云仓库创建公开仓库)
  repo: #公开仓库可直接填写用户名
  gitment_oauth:
    client_id: ''           #client ID
    client_secret: ''       #client secret
```

###### 渲染	预览	初始化

重新渲染，预览 

分别执行 `hexo clean`  `hexo g`  `hexo s`

在`localhost:4000`下预览

<font color=red>注意：每篇博客需要登录gitee账号初始化评论后才能使用</font>

#### 其他方式(推荐)

使用上述方法，评论区域的ui并不美观，可选择`yilia-plus`主题替代

###### 使用yilia-plus主题

[hexo-theme-yilia-plus](https://github.com/JoeyBling/hexo-theme-yilia-plus)

克隆地址：https://github.com/JoeyBling/hexo-theme-yilia-plus.git

这个主题支持giteement评论功能，只需要注册好gitee第三方应用然后直接进行配置，无需手动添加相关文件