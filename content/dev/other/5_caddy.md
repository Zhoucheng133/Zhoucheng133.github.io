---
title: "Caddy"
weight: 5
type: docs
---
相比`nginx`，Caddy配置更加简单，而且支持自动SSL

## 安装

对于Debian、Ubuntu、Raspbian可以通过下面的命令安装

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

可以通过下面的命令验证安装

```bash
caddy version
```

## 使用

Caddy的配置文件位于`/etc/caddy/Caddyfile`

### 反向代理

```conf
example.com {
    # 反向代理5000端口
    reverse_proxy localhost:5000
}
```

### 静态网页

```conf
example.com {
    # 这里的/var/www/example替换为静态网页目录
    root * /var/www/example
    file_server
}
```

### 组合

```conf
example.com {
    # 这里的/var/www/example替换为静态网页目录
    root * /var/www/example
    file_server
    try_files {path} /index.html

    # /api 路由使用反向代理至3000端口
    handle /api/* {
        reverse_proxy localhost:3000
    }
}
```

> [!warning]
> 如果后端路径没有自带/api路径，使用`handle_path`而不是`handle`

||请求地址|转发地址|
|-|-|-|
handle|example.com/api/test|localhost:3000/api/test
handle_path|example.com/api/test|localhost:3000/test

## 添加扩展

Caddy的所有扩展可以在[这里](https://caddyserver.com/download)找到

```bash
sudo caddy add-package <链接地址>
```

比如

```bash
sudo caddy add-package github.com/caddy-dns/alidns
```

## 重启Caddy

```bash
caddy reload
# 或者
sudo systemctl reload caddy
```