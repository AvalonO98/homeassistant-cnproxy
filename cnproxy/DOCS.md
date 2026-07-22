# CNProxy 使用文档

## 概述

CNProxy 是一个专为国内 Home Assistant 用户设计的网络代理 add-on。它提供两个核心服务：

1. **HTTP/HTTPS 正向代理**（端口 8888）：为标准代理协议，可被 Home Assistant Supervisor、Core 和其他 add-on 使用
2. **GitHub 反向加速代理**（端口 8889）：自动将 GitHub 文件请求通过镜像站代理

## 为什么需要这个 add-on？

在中国大陆访问 GitHub 经常会遇到速度慢、连接超时等问题，这直接影响 Home Assistant 的：

- **Supervisor 更新**（从 GitHub Releases 下载）
- **Core 更新**（从 GitHub Releases 下载）
- **HACS 插件安装和更新**
- **Add-on 构建**（从 GitHub 拉取源码）

CNProxy 通过内置的 GitHub 镜像代理机制，让这些操作畅通无阻。

## 配置说明

### proxy_port
正向代理监听端口。默认 `8888`。

### github_port
GitHub 加速器监听端口。默认 `8889`。

### github_mirror
GitHub 镜像站地址。默认为 `https://gh.libaojin.eu.org/`。

你也可以使用其他镜像站：
- `https://ghproxy.com/`
- `https://mirror.ghproxy.com/`

如果留空，加速器将直接连接 GitHub（不推荐国内使用）。

### upstream_proxy
如果你有自己的代理服务器（如 Shadowsocks、V2Ray、Clash 等），可以在此配置上游代理地址。格式：
```
host:port
# 或
http://host:port
```

配置后，所有通过 CNProxy 的流量都会经过此上游代理。

### allow_ips
允许访问代理服务的 IP 地址列表。支持单个 IP 和 CIDR 网段：
```yaml
allow_ips:
  - "192.168.1.0/24"
  - "10.0.0.5"
```

### timeout
代理连接超时时间（秒）。默认 `600`（10分钟）。

### verbose
启用后会在日志中输出详细的调试信息。

## 配置 Home Assistant 使用代理

### 方法一：Supervisor 代理配置（推荐）

1. 进入 Home Assistant → 设置 → 系统 → 网络
2. 找到 "HTTP 代理" 配置
3. 填入：`http://cnproxy:8888`
4. 保存并重启 Supervisor

### 方法二：环境变量

在 Home Assistant 的启动环境中添加：
```bash
export http_proxy=http://cnproxy:8888
export https_proxy=http://cnproxy:8888
```

### 方法三：Docker 配置

如果使用 Docker 安装的 Home Assistant，在 Docker 配置中添加：
```yaml
environment:
  - http_proxy=http://cnproxy:8888
  - https_proxy=http://cnproxy:8888
```

## 使用 GitHub 加速器

### 直接下载

将 GitHub 文件 URL 拼接到加速端口后访问：

```bash
# 原始 URL
https://github.com/home-assistant/core/releases/download/2024.1.0/homeassistant.tar.gz

# 加速 URL
http://homeassistant.local:8889/https://github.com/home-assistant/core/releases/download/2024.1.0/homeassistant.tar.gz
```

### 在脚本中使用

```bash
# 通过加速器下载 GitHub release
GITHUB_URL="https://github.com/user/repo/releases/download/v1.0/file.tar.gz"
PROXY_URL="http://localhost:8889/${GITHUB_URL}"
curl -L -o file.tar.gz "${PROXY_URL}"
```

### 在 HACS 中使用

HACS 支持自定义 GitHub API 代理。可在 HACS 设置中配置。

## 验证代理是否工作

### 检查正向代理

```bash
# 在 Home Assistant 终端中执行
curl --proxy http://cnproxy:8888 https://httpbin.org/ip
```

### 检查 GitHub 加速器

```bash
# 访问加速器状态页面
curl http://homeassistant.local:8889/
```

## 常见问题

### Q: 代理端口被占用怎么办？
修改 `proxy_port` 和 `github_port` 配置选项即可。

### Q: 需要对外开放代理吗？
默认允许所有 IP (`0.0.0.0/0`)。如果你只需要本机使用，可以设置为 `["127.0.0.1", "172.30.0.0/24"]`。

### Q: GitHub 加速器不支持哪些请求？
GitHub 加速器是基于 HTTP 反向代理实现的，不支持 git clone 的 SSH 协议和 Docker Registry API（ghcr.io）。对于 Docker 镜像加速，建议配置 Docker daemon 的 registry mirror。

### Q: 上游代理需要认证怎么办？
目前版本暂不支持代理认证。如有需要，建议使用无需认证的代理，或将认证信息编码在 URL 中。

## 技术支持

- GitHub Issues: https://github.com/renoex/cnproxy/issues
- Home Assistant 社区: https://bbs.hassbian.com/
