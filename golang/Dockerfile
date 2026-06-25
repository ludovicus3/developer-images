FROM quay.io/devfile/base-developer-image:ubi9-latest
LABEL maintainer="Jason Galens"

LABEL name="devfile/golang-developer-image"
LABEL version="ubi9"

# Go 1.22+    - installed to /usr/bin/go
# gopls 0.16.2+ - installed to /home/tooling/go/bin/gopls and /home/tooling/go/pkg/mod/
USER 0

ENV GOBIN=/home/tooling/go/bin
ENV GO_VERSION=1.26.0
ENV PATH=$GOBIN:/usr/local/go/bin:$PATH

ARG TARGETARCH

RUN set -eux; \
    mkdir -p $GOBIN; \
    case "$TARGETARCH" in \
      amd64) GO_ARCH="amd64" ;; \
      arm64) GO_ARCH="arm64" ;; \
      ppc64le) GO_ARCH="ppc64le" ;; \
      *) echo "Unsupported architecture: $TARGETARCH" && exit 1 ;; \
    esac; \
    curl -fsSL "https://go.dev/dl/go${GO_VERSION}.linux-${GO_ARCH}.tar.gz" -o /tmp/go.tar.gz; \
    tar -C /usr/local -xzf /tmp/go.tar.gz; \
    rm -rf /tmp/go.tar.gz; \
    go version; \
    GO111MODULE=on go install golang.org/x/tools/gopls@v0.16.2; \
    chgrp -R 0 /home/tooling; \
    chmod -R g=u /home/tooling

# Create symbolic links from /home/tooling/ -> /home/user/
RUN \
  set -eux; \
  chgrp -R 0 /home/tooling; \
  chmod -R g=u /home/tooling; \
  stow . -t /home/user/ -d /home/tooling/ --no-folding; \
  cp /home/tooling/.bashrc /home/user/.bashrc; \
  cp /home/tooling/.bash_profile /home/user/.bash_profile

# Set permissions on /etc/passwd, /etc/group, /etc/pki and /home to allow arbitrary users to write
RUN \
  chgrp -R 0 /home; \
  chmod -R g=u /etc/passwd /etc/group /home /etc/pki

# cleanup dnf cache
RUN dnf -y clean all --disableplugin=subscription-manager --enablerepo='*'

USER 10001

ENV HOME=/home/user
ENV PATH=${HOME}/bin:${PATH}