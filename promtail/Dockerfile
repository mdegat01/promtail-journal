# https://hub.docker.com/_/golang
FROM golang:1.20.2-bullseye as build
# https://github.com/grafana/loki/releases
ENV LOKI_VERSION 2.7.0

WORKDIR /usr/src/loki

# Must build binary from source on system with journal support. Published binaries on release do not include this.
# https://packages.debian.org/buster/libsystemd-dev
RUN set -eux; \
    apt-get update; \
    apt-get install -qy --no-install-recommends \ 
    libsystemd-dev=247.3-7+deb11u1 \
        ; \
    git clone --depth 1 -b "v${LOKI_VERSION}" https://github.com/grafana/loki .; \
    make clean; \
    make BUILD_IN_CONTAINER=false PROMTAIL_JOURNAL_ENABLED=true promtail

# https://hub.docker.com/_/debian
FROM debian:bullseye-20221114-slim

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
