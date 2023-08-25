# https://hub.docker.com/_/golang
FROM golang:1.20.6-bullseye as build
# https://github.com/grafana/loki/releases
ENV LOKI_VERSION 2.8.4

WORKDIR /usr/src/loki

# Must build binary from source on system with journal support. Published binaries on release do not include this.
# https://packages.debian.org/buster/libsystemd-dev
RUN set -eux; \
    echo "deb http://deb.debian.org/debian bullseye-backports main" >> /etc/apt/sources.list; \
    apt-get update; \
    apt-get install -qy --no-install-recommends \
    -t bullseye-backports libsystemd-dev=252.5-2~bpo11+1; \
    \
    git clone --depth 1 -b "v${LOKI_VERSION}" https://github.com/grafana/loki .; \
    make clean; \
    make BUILD_IN_CONTAINER=false PROMTAIL_JOURNAL_ENABLED=true promtail

# https://hub.docker.com/_/debian
FROM debian:bullseye-20230814-slim

# tzdata required for the timestamp stage to work
# Backports repo required to get a libsystemd version 246 or newer which is required to handle journal +ZSTD compression
# See https://github.com/grafana/loki/blob/v2.8.4/clients/cmd/promtail/Dockerfile#L12C1-L13C121 for more details
RUN echo "deb http://deb.debian.org/debian bullseye-backports main" >> /etc/apt/sources.list
RUN apt-get update && \
  apt-get install -qy --no-install-recommends \
  tzdata ca-certificates; \
  apt-get install -qy --no-install-recommends \
  -t bullseye-backports libsystemd-dev=252.5-2~bpo11+1; \
  \
  rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

COPY --from=build /usr/src/loki/clients/cmd/promtail/promtail /usr/bin/promtail
RUN promtail --version

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
    io.hass.type="base" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="mdegat01" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="mdegat01's Home Assistant Add-ons" \
    org.opencontainers.image.authors="mdegat01" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://github.com/mdegat01/hassio-addons" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}
