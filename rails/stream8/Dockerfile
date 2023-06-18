FROM quay.io/ludovicus/base-developer-image:stream8-latest

LABEL name="devfile/rails-developer-image"
LABEL version="ubi8"

#labels for container catalog
LABEL summary="devfile rails developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-rails"
LABEL io.openshift.expose-services=""

USER 10001

# NodeJS
ENV NVM_DIR="/home/user/.nvm"
ARG NODEJS_VERSION="18.16.0"
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
RUN source /home/user/.bashrc && nvm install v${NODEJS_VERSION} && nvm alias default v$NODEJS_VERSION && nvm use v$NODEJS_VERSION && npm install --global yarn@v1.22.17
ENV PATH=$NVM_DIR/versions/node/v$NODEJS_VERSION/bin:$PATH

# Ruby
USER 0

ARG RUBY_VERSION="3.0"
ARG POSTGRESQL_VERSION="13"

RUN dnf module enable ruby:${RUBY_VERSION} postgresql:${POSTGRESQL_VERSION} -y && \
    dnf install -y ruby{,gems} rubygem-{rake,bundler} \
      gcc-c++ make gettext libxml2-devel libcurl-devel ruby-devel postgresql postgresql-libs postgresql-devel

# Set permissions on /etc/passwd and /home to allow arbitrary users to write
RUN mkdir -p /home/user && chgrp -R 0 /home && chmod -R g=u /etc/passwd /etc/group /home

# cleanup dnf cache
RUN dnf -y clean all --enablerepo='*'

USER 10001
