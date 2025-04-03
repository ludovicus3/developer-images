ARG DEBIAN_VERSION=bookworm
FROM quay.io/ludovicus/base-developer-image:${DEBIAN_VERSION}

LABEL name="devfile/rails-developer-image"
LABEL version="${DEBIAN_VERSION}"

#labels for container catalog
LABEL summary="devfile rails developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-rails"

USER 0

ARG ADDITIONAL_PACKAGES=""
RUN \
  set -eux; \
  apt-get update; \
  apt-get install -y --no-install-recommends dirmngr gpg-agent libpq5 libpq-dev ${ADDITIONAL_PACKAGES}; \
  rm -rf /var/lib/apt/lists/*

# $PROFILE_EXT contains all additions made to the bash environment
ENV PROFILE_EXT=/etc/profile.d/udi_environment.sh
RUN touch ${PROFILE_EXT} && chown 10001 ${PROFILE_EXT}

USER 10001

ENV SHELL=/bin/bash
ENV HOME=/home/tooling

# NodeJS
ENV NVM_DIR="/home/tooling/.nvm"
RUN mkdir -p ${NVM_DIR}/
ENV NODEJS_20_VERSION=20.18.1
ENV NODEJS_18_VERSION=18.20.5
ENV NODEJS_DEFAULT_VERSION=${NODEJS_18_VERSION}
ARG NVM_VERSION=v0.40.1
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/${NVM_VERSION}/install.sh | PROFILE=/dev/null bash
RUN \
  set -eux; \
  echo 'export NVM_DIR="$HOME/.nvm"' >> ${PROFILE_EXT}; \
  echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ${PROFILE_EXT}
RUN \
  set -eux; \
  source ${NVM_DIR}/nvm.sh; \
  nvm install v${NODEJS_20_VERSION}; \
  nvm install v${NODEJS_18_VERSION}; \
  nvm alias default v${NODEJS_DEFAULT_VERSION}; \
  nvm use v${NODEJS_DEFAULT_VERSION}; \
  npm install --global yarn@v1.22.22; \
  chgrp -R 0 /home/tooling; \
  chmod -R g=u /home/tooling
ENV PATH=$NVM_DIR/versions/node/v${NODEJS_DEFAULT_VERSION}/bin:$PATH
ENV NODEJS_HOME_20=$NVM_DIR/versions/node/v${NODEJS_20_VERSION}
ENV NODEJS_HOME_18=$NVM_DIR/versions/node/v${NODEJS_18_VERSION}

# Ruby
RUN mkdir -p /home/tooling/.rvm/
ENV RVM_DIR="/home/tooling/.rvm"
ARG RUBY_VERSION=3.0.0
RUN gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
RUN curl -sSL https://get.rvm.io | bash -s stable

USER 0

RUN \
  set -eux; \
  ${RVM_DIR}/bin/rvm requirements; \
  rm -rf ${RVM_DIR}/log/*; \
  rm -rf /var/lib/apt/lists/*

USER 10001
RUN \
  set -eux; \
  echo 'export RVM_DIR="$HOME/.rvm"' >> ${PROFILE_EXT}; \
  echo '[ -s "$RVM_DIR/scripts/rvm" ] && \. "$RVM_DIR/scripts/rvm"' >> ${PROFILE_EXT}
RUN \
  set -eux; \
  ${RVM_DIR}/bin/rvm pkg install openssl; \
  ${RVM_DIR}/bin/rvm install ruby-${RUBY_VERSION} --with-openssl-dir=${RVM_DIR}/usr; \
  chgrp -R 0 /home/tooling; \
  chmod -R g=u /home/tooling

USER 0

# Define user directory for binaries
RUN \
  set -eux; \
  mkdir -p /home/tooling/.local/bin; \
  chgrp -R 0 /home; \
  chmod -R g=u /home
ENV PATH="/home/user/.local/bin:$PATH"
ENV PATH="/home/tooling/.local/bin:$PATH"

RUN \
  set -eux; \
  cat ${NVM_DIR}/bash_completion > /usr/share/bash-completion/completions/nvm; \
  cat ${RVM_DIR}/scripts/completion > /usr/share/bash-completion/completions/rvm

# Create symbolic links from /home/tooling/ -> /home/user/
RUN \
  set -eux; \
  stow . -t /home/user/ -d /home/tooling/ --no-folding; \
  cp /home/tooling/.bashrc /home/user/.bashrc; \
  cp /home/tooling/.bash_profile /home/user/.bash_profile

# Set permissions on /etc/passwd and /home to allow arbitrary users to write
RUN \
  set -eux; \
  chgrp -R 0 /home; \
  chmod -R g=u /etc/passwd /etc/group /home

USER 10001

ENV HOME=/home/user
