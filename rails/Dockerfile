FROM quay.io/ludovicus/base-developer-image:bookworm-latest

LABEL name="devfile/rails-developer-image"
LABEL version="bookworm"

#labels for container catalog
LABEL summary="devfile rails developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-rails"

USER 0

RUN apt-get install -y libpq-dev

USER 10001

ENV HOME=/home/tooling

# NodeJS
RUN mkdir -p /home/tooling/.nvm/

ENV NVM_DIR="/home/tooling/.nvm"
ARG NODEJS_VERSION=18.16.0
ARG NVM_VERSION=0.39.5
RUN curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v${NVM_VERSION}/install.sh | bash
RUN echo 'export NVM_DIR="$HOME/.nvm"' >> /home/tooling/.bash_profile && \
    echo '[ -s "$NVM_DIR/nvm.sh" ] && source "$NVM_DIR/nvm.sh"' >> /home/tooling/.bash_profile
RUN source ${NVM_DIR}/nvm.sh && \
    nvm install v${NODEJS_VERSION} && \
    nvm alias default v$NODEJS_VERSION && \
    nvm use v$NODEJS_VERSION && \
    npm install --global yarn@v1.22.17 && \
    chgrp -R 0 /home/tooling && chmod -R g=u /home/tooling
ENV PATH=$NVM_DIR/versions/node/v$NODEJS_VERSION/bin:$PATH

# Ruby
RUN mkdir -p /home/tooling/.rvm/
ENV RVM_DIR="/home/tooling/.rvm"
ARG RUBY_VERSION=3.0.0
RUN gpg --keyserver hkp://keyserver.ubuntu.com --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
RUN curl -sSL https://get.rvm.io | bash -s stable

USER 0

RUN ${RVM_DIR}/bin/rvm requirements
RUN rm -rf ${RVM_DIR}/log/*

USER 10001
RUN source ${RVM_DIR}/scripts/rvm && \
    rvm pkg install openssl && \
    rvm install ruby-${RUBY_VERSION} --with-openssl-dir=${RVM_DIR}/usr && \
    chgrp -R 0 /home/tooling && chmod -R g=u /home/tooling

USER 0

# Define user directory for binaries
RUN mkdir -p /home/tooling/.local/bin && \
    chgrp -R 0 /home && chmod -R g=u /home
ENV PATH="/home/user/.local/bin:$PATH"
ENV PATH="/home/tooling/.local/bin:$PATH"

RUN cat ${NVM_DIR}/bash_completion > /usr/share/bash-completion/completions/nvm && \
    cat ${RVM_DIR}/scripts/completion > /usr/share/bash-completion/completions/rvm

# Create symbolic links from /home/tooling/ -> /home/user/
RUN stow . -t /home/user/ -d /home/tooling/ --no-folding && \
    cp /home/tooling/.bashrc /home/user/.bashrc && \
    cp /home/tooling/.bash_profile /home/user/.bash_profile

# Set permissions on /etc/passwd and /home to allow arbitrary users to write
RUN chgrp -R 0 /home && chmod -R g=u /etc/passwd /etc/group /home

# cleanup apt cache
RUN apt clean

USER 10001

ENV HOME=/home/user
