---
title: "Windows常用命令"
weight: 3
type: docs
---
## 设置终端代理

打开PowerShell配置文件

```bash
code $PROFILE
```

```zsh
function proxy {
    param(
        [Parameter(Mandatory=$true)]
        [ValidateSet("on", "off")]
        [string]$action
    )

    switch ($action) {
        "on" {
            $env:HTTP_PROXY  = "http://127.0.0.1:7890"
            $env:HTTPS_PROXY = "http://127.0.0.1:7890"
            Write-Host "Use proxy: $env:HTTP_PROXY"
        }
        "off" {
            Remove-Item Env:HTTP_PROXY -ErrorAction SilentlyContinue
            Remove-Item Env:HTTPS_PROXY -ErrorAction SilentlyContinue
            Write-Host "Clear proxy"
        }
    }
}
```