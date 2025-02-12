FROM alpine:3.13 AS builder

LABEL maintainer="AnatoleLucet"

ARG BUILD_DEPS="autoconf automake cmake curl g++ gettext gettext-dev git libtool make ninja openssl pkgconfig unzip"
ARG TARGET=stable

RUN apk add --no-cache ${BUILD_DEPS} && \
  git clone https://github.com/neovim/neovim.git /tmp/neovim && \
  cd /tmp/neovim && \
	git checkout tags/${TARGET} && \
  # Patch bug in build script on stable
  # see https://github.com/neovim/neovim/commit/38145b919d160ea63c2547533595e761b40cfe45
  git cherry-pick -n 38145b919d160ea63c2547533595e761b40cfe45 && \
  make CMAKE_BUILD_TYPE=Release && \
  make CMAKE_INSTALL_PREFIX=/usr/local install

FROM alpine:3.13
COPY --from=builder /usr/local /usr/local/
# Required shared libraries
COPY --from=builder /lib/ld-musl-x86_64.so.1 /lib/
COPY --from=builder /usr/lib/libgcc_s.so.1 /usr/lib/
COPY --from=builder /usr/lib/libintl.so.8 /usr/lib/

CMD ["/usr/local/bin/nvim"]
