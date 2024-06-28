FROM quay.io/ludovicus/base-developer-image:ubi8-latest

LABEL name="devfile/puppet-developer-image"
LABEL version="ubi8"

#labels for container catalog
LABEL summary="devfile puppet developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-puppet"

USER 0

# Python
RUN dnf -y install python3.11 python3.11-devel python3.11-setuptools python3.11-pip nss_wrapper

RUN cd /usr/bin \
    && if [ ! -L python ]; then ln -s python3.11 python; fi \
    && if [ ! -L pydoc ]; then ln -s pydoc3.11 pydoc; fi \
    && if [ ! -L python-config ]; then ln -s python3.11-config python-config; fi \
    && if [ ! -L pip ]; then ln -s pip-3.11 pip; fi

RUN pip install pylint yq

RUN dnf install -y graphviz && \
    dnf clean all

# cleanup dnf cache
RUN dnf -y clean all --enablerepo='*'

USER 10001
