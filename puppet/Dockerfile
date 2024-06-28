FROM quay.io/ludovicus/base-developer-image:ubi8-latest

LABEL name="devfile/puppet-developer-image"
LABEL version="ubi8"

#labels for container catalog
LABEL summary="devfile puppet developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-puppet"

USER 0

RUN rpm -Uvh https://yum.puppet.com/puppet-tools-release-el-8.noarch.rpm \
    && dnf install -y pdk make gcc

# cleanup dnf cache
RUN dnf -y clean all --enablerepo='*'

USER 10001
