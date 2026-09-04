ARG BUILD_FROM=ghcr.io/home-assistant/base:3.24
# hadolint ignore=DL3006
FROM ${BUILD_FROM}

# Make sure bash exists before we switch the default shell to it below
RUN apk update && apk add --no-cache bash

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# Install requirements for add-on
# nodejs/npm come straight from Alpine 3.24's own repos (currently Node
# 24.x, matching this app's engines requirement), so there's no separate
# Node base image to track anymore. npm lives in the community repo.
RUN apk add --no-cache \
    gcc \
    py3-pip \
    python3 \
    sqlite \
    nginx \
    nodejs \
    npm
RUN rm -f -r /etc/nginx

# Copy root file system
COPY rootfs /

COPY --from=ghcr.io/mrks115/hass-dbstats:stable /usr/src/app /usr/src/app
COPY --from=ghcr.io/mrks115/hass-dbstats:stable /var/www/html /var/www/html
WORKDIR /usr/src/app
RUN npm ci --omit=dev --no-fund --no-audit && npm cache clean --force ## need to reinstall deps because they can be binary incompatible

# Health check
#HEALTHCHECK --start-period=10s \
#    CMD curl --fail http://127.0.0.1:3000 || exit 1

# Build arguments
ARG BUILD_ARCH
ARG BUILD_DATE
ARG BUILD_DESCRIPTION
ARG BUILD_NAME
ARG BUILD_REF
ARG BUILD_REPOSITORY
ARG BUILD_VERSION

# Set NODE_ENV environment variable
ENV NODE_ENV production

# Labels
LABEL \
    io.hass.name="${BUILD_NAME}" \
    io.hass.description="${BUILD_DESCRIPTION}" \
    io.hass.arch="${BUILD_ARCH}" \
    io.hass.type="addon" \
    io.hass.version=${BUILD_VERSION} \
    maintainer="Markus" \
    org.opencontainers.image.title="${BUILD_NAME}" \
    org.opencontainers.image.description="${BUILD_DESCRIPTION}" \
    org.opencontainers.image.vendor="Markus" \
    org.opencontainers.image.authors="Markus" \
    org.opencontainers.image.licenses="MIT" \
    org.opencontainers.image.url="https://github.com/mrks115" \
    org.opencontainers.image.source="https://github.com/${BUILD_REPOSITORY}" \
    org.opencontainers.image.documentation="https://github.com/${BUILD_REPOSITORY}/blob/main/README.md" \
    org.opencontainers.image.created=${BUILD_DATE} \
    org.opencontainers.image.revision=${BUILD_REF} \
    org.opencontainers.image.version=${BUILD_VERSION}


EXPOSE 3000
EXPOSE 6060
