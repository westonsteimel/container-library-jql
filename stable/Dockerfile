ARG VERSION="jql-v6.0.8"
ARG REVISION="a20f9d9dbf5b4c9f6a519140a6812fce1110a514"

FROM ghcr.io/westonsteimel/rust-static-builder:stable as builder

ARG VERSION
ARG REVISION

RUN git clone --branch "v${VERSION}" https://github.com/yamafaktory/jql /build/jql \
    && cd /build/jql \
    && git reset --hard "${REVISION}" \
    && NOCOMPRESS=1 /home/builder/build.sh

FROM alpine:latest as config

RUN addgroup jql \
    && adduser -G jql -s /bin/sh -D jql

FROM scratch

ARG VERSION
ARG REVISION
ENV JQL_VERSION="${VERSION}" \
    JQL_REVISION="${REVISION}" \
    TERM="xterm-256color"

COPY --from=config /etc/passwd /etc/passwd
COPY --from=builder /build/artifacts/jql /usr/local/bin/jql

USER jql

ENTRYPOINT ["/usr/local/bin/jql"]
CMD ["--help"]

LABEL org.opencontainers.image.title="jql" \
    org.opencontainers.image.description="jql in a container" \
    org.opencontainers.image.revision="${REVISION}" \
    org.opencontainers.image.version="${VERSION}"

