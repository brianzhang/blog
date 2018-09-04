title: openresty+nginx 实践
author: brian
date: 2018-08-30 19:44:49
tags:
---
##### Openresty Nginx 实践
----

> 首先我们先了解一下Openresty

	OpenResty® 是一个基于 Nginx 与 Lua 的高性能 Web 平台，其内部集成了大量精良的 Lua 库、第三方模块以及大多数的依赖项。用于方便地搭建能够处理超高并发、扩展性极高的动态 Web 应用、Web 服务和动态网关。

    OpenResty® 通过汇聚各种设计精良的 Nginx 模块（主要由 OpenResty 团队自主开发），从而将 Nginx 有效地变成一个强大的通用 Web 应用平台。这样，Web 开发人员和系统工程师可以使用 Lua 脚本语言调动 Nginx 支持的各种 C 以及 Lua 模块，快速构造出足以胜任 10K 乃至 1000K 以上单机并发连接的高性能 Web 应用系统。

    OpenResty® 的目标是让你的Web服务直接跑在 Nginx 服务内部，充分利用 Nginx 的非阻塞 I/O 模型，不仅仅对 HTTP 客户端请求,甚至于对远程后端诸如 MySQL、PostgreSQL、Memcached 以及 Redis 等都进行一致的高性能响应。
> 官方中文地址： [openresty](https://openresty.org/cn/)

```bash
# 1. 安装(Ubuntu)
> apt-get install libpcre3-dev \
    libssl-dev perl make build-essential curl

# 导入 GPG 密钥：
> wget -qO - https://openresty.org/package/pubkey.gpg | sudo apt-key add -
# 安装 add-apt-repository 命令
> sudo apt-get -y install software-properties-common
# 添加官方 official APT 仓库：
> sudo add-apt-repository -y "deb http://openresty.org/package/ubuntu $(lsb_release -sc) main"

# 更新 APT 索引：
> sudo apt-get update

# 安装openresty
> sudo apt-get install openresty
```

> 如果你的环境默认安装了nginx请在安装 openresty 之前停止nginx运行，再执行安装命令，否则会出现模块安装失败

	好了，通过以上步骤后，你的服务器就安装好openresty了，可以通过路径查看
 
```bash
# 默认安装路径
> cd /usr/local/openresty
# nginx模块目录
> cd /usr/local/openresty/nginx

# 启动nginx
> cd /usr/local/openresty/nginx/sbin/
> ./nginx # 执行启动
```

	nginx启动后可通过ip访问，可以查看到nginx response headers中的server
    已经变成：openresty
    
> 可以通过配置nginx 中.conf文件来实现openresty lua的执行

```bash
  location /lua {
    default_type text/html;
    content_by_lua '
      ngx.say("<p>hello, lua openresty</p>")
    ';
  }
```

> 配置完后，重新加载nginx配置

访问 /lua 这个地址，会发现页面打开后会看到 "hello, lua openresty" 这样一个页面[demo](https://netmad.me/lua)

好啦，一个简单的openresty + nginx的应用就搞定了

相关资料

[OpenResty Git](https://github.com/openresty/)

[agentzh-nginx](https://openresty.org/download/agentzh-nginx-tutorials-en.html)