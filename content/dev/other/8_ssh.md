---
title: "ssh和sftp"
weight: 8
type: docs
---

## 修改ssh密码

```bash
passwd
```

如果是制定用户名的密码

```bash
sudo passwd 用户名
```

## 创建一个sftp

可以使用docker创建

> [!IMPORTANT]
> 注意用户名和目录映射关系

```bash
# 可以通过这个命令先随机生成一个密码
openssl rand -base64 16

docker run \
-v <映射本地的地址>/<某个目录>:/home/<用户名>/<某个目录> \
--name ssh \
-p <端口号>:22 \
-d atmoz/sftp \
<用户名>:<密码>
```