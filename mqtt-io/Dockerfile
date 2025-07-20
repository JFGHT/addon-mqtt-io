ARG BUILD_FROM=ghcr.io/hassio-addons/base:15.0.9
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Copy Python requirements file
COPY requirements.txt /tmp/

# Setup base
# hadolint ignore=DL3003,DL3042
# hadolint ignore=DL3003,DL3042
RUN apk update && apk upgrade --no-cache && \
    \
    # dependencias de compilación (pin mayor con ~)
    apk add --no-cache --virtual .build-dependencies \
        build-base~0 \
        git~2 \
        libffi-dev~3 \
        py3-wheel~0 \
        python3-dev~3.11 && \
    \
    # paquetes runtime
    apk add --no-cache \
        py3-bcrypt~4 \
        py3-cryptography~41 \
        py3-pip~23 \
        python3~3.11 && \
    \
    # pip y requirements
    pip install --no-cache-dir --upgrade pip && \
    pip install --no-cache-dir -r /tmp/requirements.txt && \
    pip install --no-cache-dir 'Adafruit_DHT==1.4.0' --install-option="--force-pi2" && \
    \
    # limpieza de tests y byte-code
    find /usr \
         \( -type d -a -name test -o -name tests -o -name '__pycache__' \) \
         -o \( -type f -a -name '*.pyc' -o -name '*.pyo' \) \
         -exec rm -rf '{}' + && \
    \
    # quitamos las build-deps
    apk del --no-cache --purge .build-dependencies


# Copy root filesystem
COPY rootfs /

# Build arguments
ARG BUILD_ARCH
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
    maintainer="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Home Assistant Community Add-ons" \
    org.opencontainers.image.authors="Franck Nijhof <frenck@addons.community>" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://addons.community" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
