---
title: "Linux常用命令"
weight: 1
type: docs
---
## 查看IP地址

- IPv4地址

  ```bash
  curl 4.wsmdn.top
  ```

- IPv6地址

  ```bash
  curl 6.wsmdn.top
  ```

## 搜索进程

```bash
ps aux | grep <进程名称>
```

得到的结果**第二列**为进程ID，可以使用命令来销毁
```bash
kill <进程ID>
```

## 进程管理
可以使用htop
```bash
# 如果没有安装的话
sudo apt install htop

sudo htop
```

## 后台执行进程
可以使用screen

### 安装
```bash
sudo apt install screen
```

### 新建进程
```bash
sudo screen -S <进程名称>
```

### 查看进程
```bash
sudo screen -r <进程名称>
```