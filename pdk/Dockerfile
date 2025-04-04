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

RUN pdk env >> ${PROFILE_EXT}

# Create symbolic links from /home/tooling/ -> /home/user/
RUN stow . -t /home/user/ -d /home/tooling/ --no-folding

# Set permissions on /etc/passwd, /etc/group, /etc/pki and /home to allow arbitrary users to write
RUN \
  chgrp -R 0 /home; \
  chmod -R g=u /etc/passwd /etc/group /home /etc/pki

# cleanup dnf cache
RUN dnf -y clean all --enablerepo='*'

USER 10001

ENV HOME=/home/user