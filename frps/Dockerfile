# https://contributors.home-assistant.io/apps/guides/dockerfile/
ARG BUILD_FROM=ghcr.io/home-assistant/base:3.24
FROM ${BUILD_FROM}

ARG FRP_VERSION=0.72.0
ARG TARGETARCH
RUN \
    if [ -z "${TARGETARCH}" ]; then \
        echo "TARGETARCH is not set, please use Docker BuildKit for the build." && exit 1; \
    fi \
    && case "${TARGETARCH}" in \
        amd64) frp_arch="amd64" ;; \
        arm64) frp_arch="arm64" ;; \
        *) echo "Unsupported TARGETARCH: ${TARGETARCH}" && exit 1 ;; \
    esac \
    && frp_name="frp_${FRP_VERSION}_linux_${frp_arch}" \
    && curl -sSLf -o /tmp/frp.tar.gz \
        "https://github.com/fatedier/frp/releases/download/v${FRP_VERSION}/${frp_name}.tar.gz" \
    && tar -xzf /tmp/frp.tar.gz -C /tmp \
    && install -m 0755 "/tmp/${frp_name}/frps" /usr/bin/frps \
    && rm -rf /tmp/frp.tar.gz "/tmp/${frp_name}" \
    && frps --version

# Copy root filesystem
COPY rootfs /

LABEL \
    org.opencontainers.image.title="Home Assistant App: FRP Server" \
    org.opencontainers.image.description="frp tunnel server (frps), a self-hosted ngrok alternative" \
    org.opencontainers.image.source="https://github.com/bjorngadde/fast-reverse-proxy-ha-app" \
    org.opencontainers.image.licenses="Apache License 2.0"
