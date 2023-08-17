FROM quay.io/ludovicus/base-developer-image:stream8-latest

LABEL name="devfile/puppet-developer-image"
LABEL version="stream8"

#labels for container catalog
LABEL summary="devfile puppet developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-puppet"
LABEL io.openshift.expose-services=""

USER 0

RUN rpm -Uvh https://yum.puppet.com/puppet-tools-release-el-8.noarch.rpm \
    && dnf install -y pdk

# Set permissions on /etc/passwd and /home to allow arbitrary users to write
RUN mkdir -p /home/user && chgrp -R 0 /home && chmod -R g=u /etc/passwd /etc/group /home

# cleanup dnf cache
RUN dnf -y clean all --enablerepo='*'

USER 10001