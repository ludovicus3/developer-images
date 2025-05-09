FROM quay.io/devfile/base-developer-image:ubi9-latest
LABEL maintainer="Jason Galens"

LABEL name="devfile/rails-developer-image"
LABEL version="ubi9"

USER 0

RUN \
  dnf install -y libpq libpq-devel libyaml libyaml-devel

# $PROFILE_EXT contains all additions made to the bash environment
ENV PROFILE_EXT=/etc/profile.d/udi_environment.sh
RUN \
  touch ${PROFILE_EXT}; \
  chown 10001 ${PROFILE_EXT}

USER 10001

# We install everything to /home/tooling/ as /home/user/ may get overriden, see github.com/eclipse/che/issues/22412
ENV HOME=/home/tooling

# NodeJS
RUN mkdir -p /home/tooling/.nvm/
ENV NVM_DIR="/home/tooling/.nvm"
ENV NODEJS_20_VERSION=20.18.1
ENV NODEJS_18_VERSION=18.20.5
ENV NODEJS_DEFAULT_VERSION=${NODEJS_18_VERSION}
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.1/install.sh | PROFILE=/dev/null bash
RUN echo 'export NVM_DIR="$HOME/.nvm"' >> ${PROFILE_EXT} \
    && echo '[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"' >> ${PROFILE_EXT}
RUN source /home/user/.bashrc && \
    nvm install v${NODEJS_20_VERSION} && \
    nvm install v${NODEJS_18_VERSION} && \
    nvm alias default v${NODEJS_DEFAULT_VERSION} && nvm use v${NODEJS_DEFAULT_VERSION} && \
    npm install --global yarn@v1.22.22 &&\
    chgrp -R 0 /home/tooling && chmod -R g=u /home/tooling
ENV PATH=$NVM_DIR/versions/node/v${NODEJS_DEFAULT_VERSION}/bin:$PATH
ENV NODEJS_HOME_20=$NVM_DIR/versions/node/v${NODEJS_20_VERSION}
ENV NODEJS_HOME_18=$NVM_DIR/versions/node/v${NODEJS_18_VERSION}

USER 0

# Ruby
ENV RVM_DIR="/usr/local/rvm"
ENV RUBY_VERSION=3.0.0
RUN \
  set -eux; \
  gpg2 --keyserver hkp://keyserver.ubuntu.com --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB; \
  curl -sSL https://raw.githubusercontent.com/rvm/rvm/master/binscripts/rvm-installer | bash -s stable; \
  dnf install -y https://www.rpmfind.net/linux/centos-stream/9-stream/AppStream/x86_64/os/Packages/bison-3.7.4-5.el9.x86_64.rpm; \
  dnf install -y https://www.rpmfind.net/linux/centos-stream/9-stream/AppStream/x86_64/os/Packages/readline-devel-8.1-4.el9.x86_64.rpm; \
  ${RVM_DIR}/bin/rvm requirements; \
  ${RVM_DIR}/bin/rvm pkg install openssl; \
  usermod -a -G rvm user; \
  rm -rf ${RVM_DIR}/log/*; \
  dnf clean all --enablerepo='*'; \
  echo "export RVM_DIR=\"${RVM_DIR}\"" >> ${PROFILE_EXT}; \
  echo '[ -s "$RVM_DIR/scripts/rvm" ] && \. "$RVM_DIR/scripts/rvm"' >> ${PROFILE_EXT}

USER 10001
RUN \
  set -eux; \
  ${RVM_DIR}/bin/rvm install ruby-${RUBY_VERSION} --with-openssl-dir=${RVM_DIR}/usr; \
  ${RVM_DIR}/bin/rvm user rubies; \
  ${RVM_DIR}/bin/rvm user gemsets; \
  ${RVM_DIR}/bin/rvm use ruby-${RUBY_VERSION} --default

USER 0

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
