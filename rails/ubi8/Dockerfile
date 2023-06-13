# syntax=docker/dockerfile:1.3-labs

# updateBaseImages.sh can't operate on SHA-based tags as they're not date-based or semver-sequential, and therefore cannot be ordered
FROM quay.io/devfile/base-developer-image:ubi8-latest
LABEL maintainer="Red Hat, Inc."

LABEL com.redhat.component="devfile-universal-container"
LABEL name="devfile/rails-developer-image"
LABEL version="ubi8"

#label for EULA
LABEL com.redhat.license_terms="https://www.redhat.com/en/about/red-hat-end-user-license-agreements#UBI"

#labels for container catalog
LABEL summary="devfile universal developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-universal"
LABEL io.openshift.expose-services=""

USER 10001

# NodeJS
ENV NVM_DIR="/home/user/.nvm"
ARG NODEJS_VERSION=18.16.0
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
RUN source /home/user/.bashrc && nvm install v${NODEJS_VERSION} && nvm alias default v$NODEJS_VERSION && nvm use v$NODEJS_VERSION && npm install --global yarn@v1.22.17
ENV PATH=$NVM_DIR/versions/node/v$NODEJS_VERSION/bin:$PATH

# Ruby
ARG RUBY_VERSION=3.0
ARG POSTGRESQL_VERSION=13

USER 0

RUN dnf module enable -y ruby:${RUBY_VERSION} postgresql:${POSTGRESQL_VERSION}
RUN dnf install postgresql-libs ruby{,gems} rubygem-{rake,bundler} \
      npm nc hostname redhat-rpm-config git git-lfs postgresql \
      sudo gcc-c++ make bzip2 gettext tar vim \
      libxml2-devel libcurl-devel ruby-devel postgresql-devel

# Set permissions on /etc/passwd and /home to allow arbitrary users to write
RUN mkdir -p /home/user && chgrp -R 0 /home && chmod -R g=u /etc/passwd /etc/group /home

# cleanup dnf cache
RUN dnf -y clean all --enablerepo='*'

USER 10001
