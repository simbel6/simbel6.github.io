---
title: mac配置免密登录阿里云服务器
abbrlink: 55e9e5d7
date: 2020-04-12 15:50:53
categories: Mac
tags: 
  - mac
  - 阿里云
  - linux
  - ssh
---


## 生成一个新的 ssh key

使用 ssh-keygen 可以生成配对的 id_rsa 与 id_rsa.pub 文件
```bash
# 生成一个 ssh-key
# -t: 可选择 dsa | ecdsa | ed25519 | rsa | rsa1，代表加密方式
# -C: 注释，一般写自己的邮箱
$ ssh-keygen -t rsa -C "abc@email.cn"

# 查看生成的 id_rsa/id_rsa.pub: 配对的私钥与公钥
$ ls ~/.ssh
authorized_keys  config  id_rsa  id_rsa.pub  known_hosts
```

## 配置ssh config
```bash
vi ~/.ssh/config

# 修改 ssh 配置文件 ~/.ssh/config
Host ali
    HostName 112.*.*.* # 你的阿里云外网IP
    User root
```

## 免密登录：public-key 与 ssh-copy-id
```bash
# 在本地环境进行操作
# 执行命令后会提示你输入密码，成功之后可以直接 ssh 进去
$ ssh-copy-id ali
```

## 配置成功之后直接 ssh Host 名称就可以登录服务器了
```bash
ssh ali
```

## 禁用密码登录
修改云服务器的 ssh 配置文件：/etc/ssh/ssh_config。

PasswordAuthentication 设置为 no，禁用密码登录

```bash
# 禁用密码登录
Host *
  PasswordAuthentication no
```