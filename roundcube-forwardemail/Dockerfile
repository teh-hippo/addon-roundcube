ARG BUILD_FROM=ghcr.io/hassio-addons/base:20.0.4
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

ARG BUILD_ARCH=amd64

# hadolint ignore=DL3018
RUN apk add --no-cache \
    git \
    composer=2.9.7-r0 \
    openssl=3.5.6-r0 \
    php84=8.4.20-r1 \
    php84-curl=8.4.20-r1 \
    php84-openssl=8.4.20-r1 \
    php84-phar=8.4.20-r1

COPY --chmod=755 rootfs /

HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
    CMD pgrep -f "forwardemail-watchdog" > /dev/null || exit 1

ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="teh-hippo" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="teh-hippo" \
    org.opencontainers.image.authors="teh-hippo" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://github.com/teh-hippo/addon-roundcube" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
