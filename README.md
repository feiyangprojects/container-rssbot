## RssBot

### Overview

Minimalistic opinionated Telegram RSS bot container image based on Apline linux.

### Build

```
ARGS=()
for i in $(find ./VERSIONS/ -type f); do
  ARGS+=('--build-arg' "${i##*/}=$(< $i)")
done
docker build --build-arg LOCALE=$LOCALE "${ARGS[@]}" --tag ${PWD##*/} \
       --label org.opencontainers.image.created="$(date --rfc-3339 seconds --utc)" \
       --label org.opencontainers.image.version=$(< DISPLAY_VERSION) \
       --label org.opencontainers.image.revision=$(git rev-parse HEAD) .
```

Push image to registry:

```
docker tag ${PWD##*/} $CONTAINER_REGISTRY_USERNAME/${PWD##*/}:$(< DISPLAY_VERSION)-$LOCALE
docker tag ${PWD##*/} $CONTAINER_REGISTRY_USERNAME/${PWD##*/}:latest-$LOCALE
docker push --all-tags $CONTAINER_REGISTRY_USERNAME/${PWD##*/}
```

### Development

Since this is not only a packaging project, here is the development tips for adding new changes:

1. Put upstream source code repository to `$PROJECT_ROOT/source` is recommended.
2. Since only tarball is used for building images, patches should be generated with `git format-patch --src-prefix=rssbot-$DISPLAY_VERSION/` to make them works with `patch` command.

### Environment variables

| Name | Default value | Description |
| --- | --- | --- |
| BOT_TOKEN | - | Telegram bot token |
| MAX_INTERVAL | 3600 | Maximum fetch interval |
| MIN_INTERVAL | 300 | Minimum fetch interval |
| HTTP_PROXY | - | Proxy for HTTP connections |
| HTTPS_PROXY | - | Proxy for HTTPS connections |
| RSSBOT_DONT_PROXY_FEEDS | - | Only use proxy for Telegram bot API |

### Run

**Warning: Due to huge resources usage caused by the Rust compiler, pre-built arm64 images are unavailable.**

```
docker run --detach \
       --restart always \
       --env KEY=VALUE \
       --volume $PATH_TO_DATA:/data \
       ghcr.io/fei1yang/rssbot:latest-$LOCALE
```

Note: [Podman](https://podman.io/) is recommended for use this container image due to its amazing automatic update feature, please refer to the [official document](https://docs.podman.io/en/latest/markdown/podman-auto-update.1.html) for further details.
