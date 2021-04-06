ARG BUILD_FROM=amd64/debian:buster-20210329-slim

# https://github.com/grafana/loki/releases
FROM golang:1.16.3-buster as build
ENV LOKI_VERSION 2.2.1

# Must build binary from source on system with journal support. Published binaries on release do not include this.
RUN set -eux; \
    echo "deb http://deb.debian.org/debian buster-backports main" >> /etc/apt/sources.list; \
    apt-get update; \
    apt-get install -qy --no-install-recommends \ 
        -t buster-backports \
        libsystemd-dev=247.3-3~bpo10+1 \
        ; \
    curl -J -L -o /tmp/loki.tar.gz "https://github.com/grafana/loki/archive/refs/tags/v${LOKI_VERSION}.tar.gz"; \
    mkdir -p /src; \
    tar -xf /tmp/loki.tar.gz -C /src; \
    mv "/src/loki-${LOKI_VERSION}" /src/loki;

WORKDIR /src/loki
RUN make BUILD_IN_CONTAINER=false promtail

# hadolint ignore=DL3006
FROM ${BUILD_FROM}

COPY --from=build /src/loki/cmd/promtail/promtail /usr/bin/promtail