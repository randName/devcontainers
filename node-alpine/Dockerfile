FROM alpine:3.18

LABEL org.opencontainers.image.source=https://github.com/randName/devcontainers
LABEL org.opencontainers.image.description="Node.js on Alpine"
LABEL org.opencontainers.image.licenses=MIT

ARG NODE_VERSION=20.0.0

ARG MAKE_CORES=4

RUN apk update \
	&& apk add --no-cache \
		curl \
		libstdc++ \
	&& apk add --no-cache \
		--virtual .build-deps-full \
		linux-headers binutils-gold \
		make g++ gcc libgcc python3 \
	&& curl -fsSL --compressed -o node-src.tar.xz \
		"https://nodejs.org/dist/v$NODE_VERSION/node-v$NODE_VERSION.tar.xz" \
	&& tar -xf node-src.tar.xz \
	&& cd "node-v$NODE_VERSION" \
	&& ./configure --with-intl=small-icu \
	&& make -j$MAKE_CORES V= \
	&& make install \
	&& cd .. && rm -Rf node-v$NODE_VERSION && rm node-src.tar.xz \
	&& apk del .build-deps-full
