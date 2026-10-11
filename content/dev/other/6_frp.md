---
title: "Frp"
weight: 6
type: docs
---

`frp` 是一个内网穿透的工具，分为两个部分
- 客户端（提供服务的设备，不需要公网地址）
- 服务器端（穿透供访问的设备，需要公网地址）

## 使用

### 服务器端

1. 生成一个`token`密钥，可以通过`openssl`随机生成一个:
   ```bash
   openssl rand -hex 32
   ```
2. 创建配置文件，随便找一个位置，比如`/opt/frp/frps.toml`
   ```bash
   sudo mkdir -p /opt/frp
   cd /opt/frp
   sudo nano /opt/frp/frps.toml
   ```
   其中配置信息如下
   ```toml
   # 转发端口，可以任意，但是注意要在服务器端开放这个端口供访问
   bindPort = 7000
   proxyBindAddr = "127.0.0.1"

   auth.method = "token"
   auth.token = "替换成openssl生成的密钥"
   ```
   保存并退出`Ctrl`/`control` + `O`，然后`Ctrl`/`control` + `X`
3. 运行frp服务器端容器
   ```bash
   sudo docker pull ghcr.io/fatedier/frps:v0.71.0
   sudo docker run -d \
   --name frps \
   --restart unless-stopped \
   --network host \
   -v /opt/frp/frps.toml:/etc/frp/frps.toml:ro \
   ghcr.io/fatedier/frps:v0.71.0 \
   -c /etc/frp/frps.toml
   ```
4. 查看frp服务器端运行状态
   ```bash
   sudo docker logs frps
   ```

### 客户端
1. 创建配置文件，随便找一个位置，比如`~/frp/frpc.toml`
   ```bash
   nano ~/frp/frpc.toml
   ```
   其中配置如下
   ```toml
   serverAddr = "你的云服务器公网IPv4"
   # 转发端口，和服务器端匹配
   serverPort = 7000

   auth.method = "token"
   auth.token = "与云服务器完全相同的密钥"

   [[proxies]]
   # 名字，任意
   name = "frp-service"
   type = "tcp"
   localIP = "127.0.0.1"
   # 这是客户端需要穿透的端口号
   localPort = 8123
   # 这是挂载到服务器的端口号，任意
   remotePort = 18123
   ```
2. 运行frp客户端端容器
   ```bash
   sudo docker pull ghcr.io/fatedier/frpc:v0.71.0
   sudo docker run -d \
   --name frpc \
   --restart unless-stopped \
   --network host \
   -v "$HOME/frp/frpc.toml:/etc/frp/frpc.toml:ro" \
   ghcr.io/fatedier/frpc:v0.71.0 \
   -c /etc/frp/frpc.toml
   ```
3. 查看日志
   ```bash
   sudo docker logs frpc
   ```
  
### 反代理

见[Caddy](/dev/other/5_caddy/)文章