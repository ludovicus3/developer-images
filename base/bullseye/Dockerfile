ARG DEBIAN_VERSION=stable
FROM debian:${DEBIAN_VERSION}

LABEL name="ludovicus/base-development-image"
LABEL version="${DEBIAN_VERSION}"

#labels for container catalog
LABEL summary="devfile base developer image"
LABEL description="Image with base developers tools. Languages SDK and runtimes excluded."
LABEL io.k8s.display-name="devfile-developer-base"

USER 0

RUN apt update
RUN apt upgrade -y
RUN apt install -y bash curl diffutils git git-lfs iproute2 jq less lsof man nano procps p7zip-full \
                   libdigest-sha-perl net-tools openssh-client rsync socat sudo time vim wget zip gpg
RUN apt clean

# make /bin/sh symlink to bash instead of dash
RUN echo "dash dash/sh boolean false" | debconf-set-selections
RUN DEBIAN_FRONTEND=noninteractive dpkg-reconfigure dash

COPY --chown=0:0 entrypoint.sh /
RUN \
    # add user and configure it
    useradd -u 10001 -G sudo,root -d /home/user --shell /bin/bash -m user && \
    # Setup $PS1 for a consistent and reasonable prompt
    echo "export PS1='\W \`git branch --show-current 2>/dev/null | sed -r -e \"s@^(.+)@\(\1\) @\"\`$ '" >> /home/user/.bashrc && \
    # Copy the global git configuration to user config as global /etc/gitconfig
    #  file may be overwritten by a mounted file at runtime
    cp /etc/gitconfig /home/user/.gitconfig && \
    # Set permissions on /etc/passwd and /home to allow arbitrary users to write
    chgrp -R 0 /home && \
    chmod -R g=u /etc/passwd /etc/group /home && \
    chmod +x /entrypoint.sh

USER 10001
ENV HOME=/home/user
WORKDIR /projects
ENTRYPOINT [ "/entrypoint.sh" ]
CMD ["tail", "-f", "/dev/null"]
