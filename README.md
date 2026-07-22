# CNProxy - China Network Proxy for Home Assistant

[![GitHub Release][releases-shield]][releases]
[![License][license-shield]](LICENSE)

🚀 **专为国内网络环境优化的 Home Assistant 代理 add-on**

让 Home Assistant 的更新、组件下载和 GitHub 访问畅通无阻。

## 功能

- ✅ **HTTP/HTTPS 正向代理** — 可作为 Supervisor、Core 和其他服务的代理
- ✅ **GitHub 加速器** — 自动将 GitHub 请求通过镜像站代理，加速下载
- ✅ **上游代理支持** — 可配置自定义代理作为上游（机场/梯子等）
- ✅ **访问控制** — 支持 IP/CIDR 白名单，保障安全
- ✅ **双端口设计** — 代理端口(8888) + GitHub 加速端口(8889)
- ✅ **中文友好** — 完整的中文界面和文档

## 安装

[![添加到 Home Assistant][addon-badge]][addon-link]

或者手动添加仓库：`[https://github.com/renoex/cnproxy](https://github.com/AvalonO98/homeassistant-cnproxy)`

## 配置

| 选项 | 说明 | 默认值 |
|------|------|--------|
| `proxy_port` | 正向代理监听端口 | `8888` |
| `github_port` | GitHub 加速器监听端口 | `8889` |
| `github_mirror` | GitHub 镜像地址 | `https://gh.libaojin.eu.org/` |
| `upstream_proxy` | 上游代理地址 | (空) |
| `allow_ips` | 允许访问的 IP/CIDR 列表 | `0.0.0.0/0` |
| `timeout` | 超时时间（秒） | `600` |
| `verbose` | 详细日志 | `false` |

## 使用方法

### 1. 配置 Home Assistant 使用代理

在 Home Assistant 的 Supervisor → 系统 → 网络 中设置：
```
HTTP 代理: http://cnproxy:8888
HTTPS 代理: http://cnproxy:8888
```

### 2. 使用 GitHub 加速器

将 GitHub URL 拼接在加速端口后：

```
原始: https://github.com/user/repo/releases/download/v1.0/file.tar.gz
加速: http://homeassistant.local:8889/https://github.com/user/repo/releases/download/v1.0/file.tar.gz
```

### 3. 配合上游代理使用

如果你已有代理服务（如 Clash/V2Ray），可配置 `upstream_proxy`：
```
上游代理: 192.168.1.100:7890
```

## 支持的 GitHub 镜像站

| 镜像站 | URL |
|--------|-----|
| gh.libaojin.eu.org | `https://gh.libaojin.eu.org/` |
| ghproxy.com | `https://ghproxy.com/` |
| mirror.ghproxy.com | `https://mirror.ghproxy.com/` |

[releases-shield]: https://img.shields.io/github/release/renoex/cnproxy.svg
[releases]: https://github.com/renoex/cnproxy/releases
[license-shield]: https://img.shields.io/github/license/renoex/cnproxy.svg
[addon-badge]: https://my.home-assistant.io/badges/supervisor_addon.svg
[addon-link]: https://my.home-assistant.io/redirect/supervisor_addon/?addon=local_cnproxy
