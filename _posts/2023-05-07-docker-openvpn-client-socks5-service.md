---
layout: post
title: Docker openvpn client socks5 service
date: 2023-05-07 13:57:46 +0800
categories: [Blog]
tags: [Docker, Openvpn, Socks]
author: sead
summary: docker ,openvpn client, socks5 proxy service
---
docker 使用openvpn client连接vpn

## 拉取 socks5 servier 镜像

```bash
docker pull serjs/go-socks5-proxy
```
## openvpn 客户端连接

- 使用ovpn文件
- 用户/密码免输入 --auth-user-pass < (echo -e "opvn_user\novpn_pass")

```console
openvpn  --config  test.ovpn --auth-user-pass < (echo -e "opvn_user\novpn_pass")
```

## /path/to/auth 用户密码文件

```bash
AUTH_FILE_PATH=~/.openvpn-auth
touch ~/.openvpn-auth
echo "username" >> ~/.openvpn-auth
echo "password" >> ~/.openvpn-auth
chmod 600 ~/.openvpn-auth
````