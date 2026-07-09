FROM quay.io/devfile/base-developer-image:ubi9-latest
LABEL maintainer="Jason Galens"

LABEL name="devfile/golang-developer-image"
LABEL version="ubi9"

USER 0

RUN set -eux; \
    dnf install -y gcc

ENV HOME /home/tooling

USER 10001

ENV CARGO_HOME /home/tooling
ENV RUSTUP_HOME /home/tooling

RUN set -eux; \
    curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y

ENV PATH ${HOME}/bin:${PATH}

USER 0

RUN set -eux; \
    chgrp -R 0 /home/tooling; \
    chmod -R g=u /home/tooling; \
    stow . -t /home/user/ -d /home/tooling/ --no-folding; \
    cp /home/tooling/.bashrc /home/user/.bashrc; \
    cp /home/tooling/.bash_profile /home/user/.bash_profile

# Set permissions on /etc/passwd, /etc/group, /etc/pki and /home to allow arbitrary users to write
RUN set -eux; \
    chgrp -R 0 /home; \
    chmod -R g=u /etc/passwd /etc/group /home /etc/pki

# cleanup dnf cache
RUN dnf -y clean all --disableplugin=subscription-manager --enablerepo='*'

USER 10001

ENV HOME /home/user
ENV PATH ${HOME}/bin:${PATH}
