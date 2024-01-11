FROM quay.io/ludovicus/base-developer-image:bookworm-latest

LABEL name="devfile/cpp-developer-image"
LABEL version="bookworm"

#labels for container catalog
LABEL summary="devfile cpp developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-cpp"

USER 10001

USER 0

RUN apt install -y build-essential clang g++ gcc clang-tidy clang-format gdb cmake ccache clangd

# Set permissions on /etc/passwd and /home to allow arbitrary users to write
RUN chgrp -R 0 /home && chmod -R g=u /etc/passwd /etc/group /home

# cleanup apt cache
RUN apt clean

USER 10001
