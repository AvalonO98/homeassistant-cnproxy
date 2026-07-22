ARG BUILD_FROM
FROM $BUILD_FROM

# 安装 tinyproxy 和 nginx
RUN apk add --no-cache \
    tinyproxy \
    nginx \
    openssl \
    curl \
    jq

# 复制 rootfs 到根目录（包含 s6-overlay 服务脚本和配置模板）
COPY rootfs /

# 确保 s6-overlay 脚本具有可执行权限
RUN chmod a+x /etc/s6-overlay/s6-rc.d/*/run /etc/s6-overlay/s6-rc.d/*/finish 2>/dev/null || true

# 创建必要的运行目录
RUN mkdir -p /run/nginx /run/tinyproxy /data/logs

WORKDIR /
