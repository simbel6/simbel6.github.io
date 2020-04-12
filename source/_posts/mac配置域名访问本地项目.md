---
title: mac 配置域名访问本地项目
categories: Mac
tags:
  - mac
  - nginx
  - hosts
abbrlink: 7f219268
date: 2020-04-12 15:37:08
---

## 打开hosts文件

```bash
sudo vi /etc/hosts
```

## hosts文件里修改127.0.0.1映射

```bash
127.0.0.1   dev.abc.com  
```

## 打开nginx配置文件

```bash
vi /usr/local/etc/nginx/nginx.conf
```

## 修改nginx配置

```bash
 server {
    listen       80;
    server_name  dev.abc.com;
    location / {
      proxy_pass http://localhost:3800;
    }
  }
```