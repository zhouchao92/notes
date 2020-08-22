---
title: docker部署vue项目
tags:
  - docker
  - vue
  - nginx
categories:
  - notes
  - 后端
abbrlink: 2d5c549e
date: 2020-08-22 10:27:51
---

在虚拟机上部署了博客后端项目（Spring Boot）后，再来部署博客后台管理中心项目（vue）。采用 nginx 镜像为基础镜像来生成项目镜像，再通过 docker 的容器进行部署。

<!--more-->

###### 项目配置与编译

在本机上通过 vue-cli 来编译项目，生成项目的资源文件。另一种方式是将项目拷贝到虚拟机上（删除不必要文件和依赖文件，传的速度会快一点，`node_modules` 这个文件夹中是依赖相关的，子文件比较多，影响传递速度），然后通过 node 和 Dockerfile 来编译生成项目资源文件。

在项目中添加编译相关的配置 `vue.config.js`（项目根目录下）

```js
module.exports = {
    // 公共路径(必须有的)，在生产环境下使用相对路径，在开发时，测试时使用绝对路径
    publicPath: process.env.NODE_ENV === 'production' ? './' : '/',
    // 输出文件目录
    outputDir: "dist",
    // 静态资源存放的文件夹(相对于ouputDir)
    assetsDir: "static",
    productionSourceMap: true,	// 资源映射
    devServer: {
        port: 9001, // 启动端口
        open: false, // 启动后是否自动打开网页
        // 代理
        proxy: {
            '/user': {
                target: 'http://192.168.134.128:9000',
                ws: true,
                changeOrigin: true,
                pathRewrite: {
                    '^/user': '' // 通过pathRewrite重写地址，将前缀/api转为/
                }
            },
            '/admin': {
                target: 'http://192.168.134.128:9000',
                ws: true,
                changeOrigin: true,
                pathRewrite: {
                    '^/admin': '' // 通过pathRewrite重写地址，将前缀/api转为/
                }
            }
        },
    },
    lintOnSave: false, // 取消 eslint 验证
};
```

通过命令 `npm run build` 即可生成项目资源文件 ，存放在`dist`目录下。

###### 构建项目镜像

将 `dist`中的文件通过xftp工具拷贝到虚拟机上，创建`nginx.conf`文件，用于配置代理，创建dockerfile文件，用于构建镜像。

`nginx.conf`

```properties
user nginx;
worker_processes auto;
# 错误日志输出目录
error_log  /var/log/nginx/error.log  warn;
pid     /var/run/nginx.pid;
events {
    worker_connections  1024;
}
http {
    server {
    	# 监听端口
        listen  80;
        # 名称可自定义
        server_name  192.168.134.128;
        # access日志
        access_log  /var/log/nginx/host.access.log;
        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
            try_files $uri $uri/ @router; 
        }
        # vue的路由配置
        location @router {
            rewrite ^.*$ /index.html last;
        }
        # 端口转发，对应axios的配置
        location /user{
            proxy_pass http://192.168.134.128:9000;	
        }
        location /admin{
            proxy_pass http://192.168.134.128:9000;
        }
    }
}
```

`Dockerfile`

```dockerfile
# 设置基础镜像
FROM nginx
# 定义作者
MAINTAINER zhouchao
# 将dist文件中的内容复制到 /usr/share/nginx/html/ 这个目录下面
COPY dist/  /usr/share/nginx/html/
# 复制配置文件 
COPY nginx.conf /etc/nginx/nginx.conf
```

docker 编译生成镜像

```bash
[root@localhost blog-manage]# docker build -t blog-manage:1.0.0 .
Sending build context to Docker daemon  6.773MB
Step 1/4 : FROM nginx
 ---> 4bb46517cac3
Step 2/4 : MAINTAINER zhouchao
 ---> Using cache
 ---> 09c5138b1196
Step 3/4 : COPY dist/  /usr/share/nginx/html/
 ---> Using cache
 ---> 9851b6f52bb4
Step 4/4 : COPY nginx.conf /etc/nginx/nginx.conf
 ---> Using cache
 ---> 6bc149edde51
Successfully built 6bc149edde51
Successfully tagged blog-manage:1.0.0

[root@localhost blog-manage]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
blog-manage         latest              6bc149edde51        3 seconds ago       139MB
```

###### 启动容器

在构建镜像成功后，以挂载的方式启动容器，方便修改配置文件，挂载后修改配置和更新项目资源不需要进入容器内部。先创建好挂载的目录和文件，再创建容器并启动。

```shell
[root@localhost blog-manage]# cd /mydata
[root@localhost blog-manage]# mkdir wwwroot
[root@localhost blog-manage]# mkdir logs
[root@localhost blog-manage]# mkdir conf
[root@localhost blog-manage]# cd conf
[root@localhost blog-manage]# touch nginx.conf
```

注意 nginx.conf 为文件，如果不创建，在创建容器时，会把它当作一个目录。

- wwwroot 存储项目资源文件
- logs 存储日志文件
- conf 存储 nginx 配置文件

创建并启动容器

```shell
docker run -d --name blog-manage --restart always -p 9001:80 \
-v /mydata/blog-manage/wwwroot:/usr/share/nginx/html \
-v /mydata/blog-manage/conf/nginx.conf:/etc/nginx/nginx.conf \
-v /mydata/blog-manage/logs:/var/log/nginx \
nginx
```

在后端项目容器启动状态下，即可访问后端管理中心项目。

> 访问地址：http://192.168.134.128:9001/

###### 踩坑记录

<i style=color:red;>Resource interpreted as Stylesheet but transferred with MIME type text/plain</i>

没有配置项目编译的相关配置，项目部署后访问 css 文件加载不出来，控制台报警告`Resource interpreted as Stylesheet but transferred with MIME type text/plain`，意思 css 文件是没有指定类型，默认加载为`text/plain`格式，通过查看 `index.html`，没有指定 css 文件的加载类型。

```html
<link href=css/xxxx.css rel=prefetch>
<link href=css/xxxx.css rel=preload as=style>
<link href=css/xxxx.css rel=stylesheet>
```

解决方案：

1. 将 `index.html`的头部声明 `<!DOCTYPE html>`去掉，这种方法确实有效，不过仍有部分css文件无法加载，而且这种方法需要每次都修改，比较麻烦。

2. 在编译配置文件中添加静态资源存放的文件夹的配置

   ```js
   // 静态资源存放的文件夹(相对于ouputDir)
   assetsDir: "static"
   ```

*<font color=red>502 Bad Gateway</font>* 

错误的网关，问题在`nginx.conf`配置出错。

*路由重定向失败 <font color=red>Redirected when going from "/login" to "/index" via a navigation guard</font>*

这个问题是由于博客后端部署错误的问题，登录成功后创建的cookie路径和域错误，导致在虚拟机 IP 下没有cookie产生，后端返回登录成功，但首页检查cookie中的token不存在，又重定向到登陆页面。

###### 经验小结

在部署后项目访问时出现各种问题，比如<font color=red>连接拒绝</font>，<font color=red>404</font>，<font color=red>502</font>等，首先可以通过查看挂载目录下的错误日志，如果是配置错误问题，一般都会输出在这里。其次，可以通过浏览器 `F12`的`network`进行检查，看看是否是后端的问题。