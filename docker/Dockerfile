# syntax=docker/dockerfile:1

ARG DEBIAN_TAG=13-slim
ARG BIRD_VERSION=3.3.2

# ------------------------------------------------------------
# Builder
# ------------------------------------------------------------
FROM debian:${DEBIAN_TAG} AS builder

ARG BIRD_VERSION

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        bison \
        build-essential \
        ca-certificates \
        curl \
        flex \
        libncurses-dev \
        libreadline-dev \
        pax-utils \
    && rm -rf /var/lib/apt/lists/*

RUN curl -fsSL \
        "https://bird.nic.cz/download/bird-${BIRD_VERSION}.tar.gz" \
        -o /usr/src/bird.tar.gz \
    && mkdir /usr/src/bird \
    && tar -xzf /usr/src/bird.tar.gz \
        --strip-components=1 \
        -C /usr/src/bird \
    && rm /usr/src/bird.tar.gz

WORKDIR /usr/src/bird

RUN ./configure \
        --prefix=/usr \
        --sysconfdir=/etc/bird \
        --localstatedir=/var \
        --runstatedir=/run/bird \
    && make -j"$(nproc)" \
    && make DESTDIR=/rootfs install

RUN lddtree \
      --copy-to-tree /rootfs \
      --bindir /usr/sbin \
      '/rootfs/usr/sbin/*'

# ------------------------------------------------------------
# Runtime
# ------------------------------------------------------------
FROM scratch AS runtime

COPY --from=builder /rootfs /

EXPOSE 179/tcp

STOPSIGNAL SIGTERM

CMD ["/usr/sbin/bird", "-c", "/etc/bird/bird.conf", "-d"]
