ARG BUILD_FROM=ghcr.io/hassio-addons/base:20.0.4
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Build arguments
ARG BUILD_ARCH=amd64
ARG ROUNDCUBE_VERSION="1.7-rc6"
ARG ROUNDCUBE_SHA256="c68a7cf44c2f23adc76b3283aadb09c3d2921bd24efec1e9e14e57927c924265"

# Install PHP, nginx, and dependencies
# hadolint ignore=DL3018
RUN apk add --no-cache \
    git \
    nginx=1.28.3-r0 \
    sqlite=3.51.2-r0 \
    composer=2.9.7-r0 \
    openssl=3.5.6-r0 \
    php84=8.4.20-r1 \
    php84-fpm=8.4.20-r1 \
    php84-ctype=8.4.20-r1 \
    php84-curl=8.4.20-r1 \
    php84-dom=8.4.20-r1 \
    php84-exif=8.4.20-r1 \
    php84-fileinfo=8.4.20-r1 \
    php84-gd=8.4.20-r1 \
    php84-iconv=8.4.20-r1 \
    php84-intl=8.4.20-r1 \
    php84-mbstring=8.4.20-r1 \
    php84-opcache=8.4.20-r1 \
    php84-openssl=8.4.20-r1 \
    php84-pdo=8.4.20-r1 \
    php84-pdo_sqlite=8.4.20-r1 \
    php84-pecl-imagick=3.8.1-r0 \
    php84-pecl-imap=1.0.3-r0 \
    php84-session=8.4.20-r1 \
    php84-simplexml=8.4.20-r1 \
    php84-sockets=8.4.20-r1 \
    php84-xml=8.4.20-r1 \
    php84-xmlwriter=8.4.20-r1 \
    php84-zip=8.4.20-r1

# Set shell for pipefail support
SHELL ["/bin/ash", "-o", "pipefail", "-c"]

# Download and verify Roundcube
RUN curl -J -L -o /tmp/roundcube.tar.gz \
        "https://github.com/roundcube/roundcubemail/releases/download/${ROUNDCUBE_VERSION}/roundcubemail-${ROUNDCUBE_VERSION}-complete.tar.gz" \
    && echo "${ROUNDCUBE_SHA256}  /tmp/roundcube.tar.gz" | sha256sum -c \
    && mkdir -p /var/www/roundcube \
    && tar -xzf /tmp/roundcube.tar.gz -C /var/www/roundcube --strip-components=1 \
    && rm -rf /tmp/roundcube.tar.gz /var/www/roundcube/installer

# Install Forward Email plugin via composer
# NOTE: Change the echo string below to force Docker to re-fetch the plugin
WORKDIR /var/www/roundcube
# hadolint ignore=DL3059
RUN echo "cache-bust-v11-db-init-tolerant" \
    && composer --version \
    && composer config --no-interaction allow-plugins.roundcube/plugin-installer true
WORKDIR /

# Copy root filesystem — force executable permissions
COPY --chmod=755 rootfs /

# Health check — verify PHP-FPM is responding (not nginx, which blocks non-ingress)
HEALTHCHECK --interval=30s --timeout=10s --start-period=30s --retries=3 \
    CMD pgrep php-fpm > /dev/null || exit 1

# Build arguments for labels
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Labels
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
