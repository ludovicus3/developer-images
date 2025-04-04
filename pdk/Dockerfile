FROM quay.io/devfile/base-developer-image:ubi9-latest
LABEL maintainer="Jason Galens"

LABEL name="devfile/pdk-developer-image"
LABEL version="ubi9"

USER 0

# $PROFILE_EXT contains all additions made to the bash environment
ENV PROFILE_EXT=/etc/profile.d/udi_environment.sh
RUN \
  touch ${PROFILE_EXT}; \
  chown 10001 ${PROFILE_EXT}

RUN \
  rpm -Uvh https://yum.puppet.com/puppet-tools-release-el-9.noarch.rpm; \
  dnf install -y pdk make gcc; \
  dnf clean all -y --enablerepo='*'

RUN pdk env >> ${PROFILE_EXT}}

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

# Create symbolic links from /home/tooling/ -> /home/user/
RUN stow . -t /home/user/ -d /home/tooling/ --no-folding

# Set permissions on /etc/passwd, /etc/group, /etc/pki and /home to allow arbitrary users to write
RUN chgrp -R 0 /home && chmod -R g=u /etc/passwd /etc/group /home /etc/pki

# cleanup dnf cache
RUN dnf -y clean all --enablerepo='*'

USER 10001

ENV HOME=/home/user