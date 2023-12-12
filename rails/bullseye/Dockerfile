# syntax=docker/dockerfile:1.3-labs

# updateBaseImages.sh can't operate on SHA-based tags as they're not date-based or semver-sequential, and therefore cannot be ordered
FROM quay.io/ludovicus/base-developer-image:bullseye-latest
LABEL maintainer="Red Hat, Inc."

LABEL com.redhat.component="devfile-rails-container"
LABEL name="devfile/rails-developer-image"
LABEL version="bullseye"

#labels for container catalog
LABEL summary="devfile rails developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-rails"
LABEL io.openshift.expose-services=""

USER 10001

# NodeJS
ENV NVM_DIR="/home/user/.nvm"
ENV NODEJS_VERSION=18.16.0
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
RUN source ${NVM_DIR}/nvm.sh && nvm install v${NODEJS_VERSION} && nvm alias default v$NODEJS_VERSION && nvm use v$NODEJS_VERSION && npm install --global yarn@v1.22.17
ENV PATH=$NVM_DIR/versions/node/v$NODEJS_VERSION/bin:$PATH

# Ruby
ENV RVM_DIR="/home/user/.rvm"
ENV RUBY_VERSION=3.0.0
RUN gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
RUN curl -sSL https://get.rvm.io | bash -s stable

USER 0
RUN ${RVM_DIR}/bin/rvm requirements

USER 10001
RUN source ${RVM_DIR}/scripts/rvm; ${RVM_DIR}/bin/rvm install ruby-${RUBY_VERSION}

USER 0

# Set permissions on /etc/passwd and /home to allow arbitrary users to write
RUN mkdir -p /home/user && chgrp -R 0 /home && chmod -R g=u /etc/passwd /etc/group /home

# cleanup apt cache
RUN apt clean

USER 10001
