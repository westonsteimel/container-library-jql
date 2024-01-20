ARG VERSION="main"
ARG REVISION="09fd0c9f6d106d2f0145402582962e2f6283da48"

FROM ghcr.io/westonsteimel/rust-static-builder:stable as builder

ARG VERSION
ARG REVISION

RUN git clone --branch "${VERSION}" https://github.com/yamafaktory/jql /build/jql \
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

