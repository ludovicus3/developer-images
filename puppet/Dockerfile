FROM quay.io/ludovicus/base-developer-image:stream9-latest

LABEL name="devfile/puppet-developer-image"
LABEL version="stream9"

#labels for container catalog
LABEL summary="devfile puppet developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-puppet"

USER 0

RUN rpm -Uvh https://yum.puppet.com/puppet-tools-release-el-9.noarch.rpm \
    && dnf install -y pdk make gcc

# cleanup dnf cache
RUN dnf -y clean all --enablerepo='*'

USER 10001
