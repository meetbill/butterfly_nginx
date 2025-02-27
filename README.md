# butterfly_nginx

<div align=center><img src="https://github.com/meetbill/butterfly/blob/master/images/butterfly.png" width="350"/></div>

蝴蝶（轻量化 Web 框架）如同蝴蝶一样，此框架小而美

```
    __          __  __            ______
   / /_  __  __/ /_/ /____  _____/ __/ /_  __
  / __ \/ / / / __/ __/ _ \/ ___/ /_/ / / / /
 / /_/ / /_/ / /_/ /_/  __/ /  / __/ / /_/ /
/_.___/\__,_/\__/\__/\___/_/  /_/ /_/\__, /
                                    /____/
```

<!-- vim-markdown-toc GFM -->

* [1 Butterfly nginx 物理部署](#1-butterfly-nginx-物理部署)
    * [1.1 操作](#11-操作)
    * [1.2 接口认证](#12-接口认证)
* [2 Butterfly nginx docker 部署](#2-butterfly-nginx-docker-部署)

<!-- vim-markdown-toc -->
## 1 Butterfly nginx 物理部署

```
webserver
├── cache
│   ├── client_body
│   ├── fastcgi
│   ├── proxy
│   ├── scgi
│   └── uwsgi
├── conf
│   ├── mime.types
│   └── nginx.conf
├── logs
│   ├── butterfly.access.log
│   ├── error.log
│   └── nginx.pid
├── run.sh
├── sbin
│   └── nginx
└── __stdout
```

### 1.1 操作

> * (1) [编译 nginx](https://github.com/meetbill/op_practice_book/blob/master/doc/web/nginx.md)
> * (2) 将编译后的 nginx 放到 webserver/sbin 目录
> * (3) 将 webserver 放到 butterfly 目录中，即

```
butterfly
├── conf
├── handlers
├── logs
├── main.py
├── run.sh
├── static
├── __stdout
├── templates
├── test
├── test_handler.py
├── third
├── webserver -------------------------- 将 webserver 放到 butterfly 项目目录下
├── wsgiapp.py
└── xlib
```

> * (4) 配置：配置 webserver/conf/nginx.conf 修改端口
> * (5) 启动：cd webserver/ & bash run.sh start

### 1.2 接口认证

> 接口认证流程图
```
NGINX ---- auth request ----> /auth/verification
                                  |         |
  |     <---      200  <------  SUCCESS    FAILED -----> 401  -----> /butterfly_401
  |
  ----> underlying request ----> BACKEND SERVER
```
[wiki](https://github.com/meetbill/butterfly-nginx/wiki/nginx_auth_request)

默认的配置中增加了 /auth/verification 认证配置

接口认证通过后会向后端添加 `X-Username` header

> 如何去掉接口认证
```
去掉 location / 中的如下几行即可:
            auth_request        /auth/verification;
            auth_request_set    $butterfly_location $upstream_http_location;
            auth_request_set    $butterfly_username $upstream_http_username;
            error_page 401    = /butterfly_401;
            proxy_set_header  X-Username        $butterfly_username;
```

## 2 Butterfly nginx docker 部署 

> 目录
```
docker_nginx
├── Dockerfile.web
├── README.md
└── conf
    └── nginx.conf
```
