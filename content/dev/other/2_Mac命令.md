---
title: "Mac常用命令"
weight: 2
type: docs
---
## 设置终端代理

```shell
export http_proxy=http://127.0.0.1:1080
export https_proxy=$http_proxy
```

检查是否处于代理模式

```shell
curl cip.cc
```

### 代理脚本
```zsh
# 注意替换端口
function proxy() {
    if [[ "$1" == "on" ]]; then
        export http_proxy="http://127.0.0.1:7890"
        export https_proxy="http://127.0.0.1:7890"
        export no_proxy="localhost,127.0.0.1"
        echo "代理已打开"
    elif [[ "$1" == "off" ]]; then
        unset http_proxy
        unset https_proxy
        unset no_proxy
        echo "代理已关闭"
    else
        echo "Usage: proxy {on|off}"
    fi
}

alias proxy='proxy'
```

## 查看IP地址

- IPv4地址

  ```bash
  curl 4.ipw.cn
  ```

- IPv6地址

  ```bash
  curl 6.ipw.cn
  ```

## 设置环境变量
 
 设置环境变量的文件: `/Users/{用户名}/.zshrc`

 添加环境变量：

 ```bash
 export PATH="path/to/your/sdk/bin:$PATH"
 ```