---
title: "Nginx 常用配置"
date: 2021-10-15T10:08:24+08:00
summary: "Nginx 常用配置 - 代理和负载均衡"
draft: false
---

## 常用配置

- 反向代理
    >
    >```CONF
    >server {
    >    listen       80;
    >    server_name  localhost;
    >
    >    location / {
    >        proxy_pass http://127.0.0.1:8080;
    >        index  index.html index.htm index.php;
    >    }
    >}
    >```

- 负载均衡

    >```CONF
    >upstream testServer {
    >  server localhost:8087 weight=10;
    >  server localhost:8088 weight=2;
    >  server localhost:8089;
    >}
    >```
    >
    >```CONF
    >location / {
    >  root   html;
    >  index  index.html index.htm;
    >  proxy_pass http://testServer; // testServer 为>自己定义的服务器集群
    >}
    >```
