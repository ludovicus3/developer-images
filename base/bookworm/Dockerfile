ARG DEBIAN_VERSION=stable
FROM debian:${DEBIAN_VERSION}

LABEL name="ludovicus/base-development-image"
LABEL version="${DEBIAN_VERSION}"

#labels for container catalog
LABEL summary="devfile base developer image"
LABEL description="Image with base developers tools. Languages SDK and runtimes excluded."
LABEL io.k8s.display-name="devfile-developer-base"

SHELL ["/bin/bash", "-c"]

USER 0

ENV HOME=/home/tooling
RUN mkdir -p /home/tooling/

RUN apt update
RUN apt upgrade -y
RUN apt install -y bash curl diffutils git git-lfs iproute2 jq less lsof man nano \
            procps p7zip-full libdigest-sha-perl net-tools openssh-client rsync \
            socat sudo time vim wget zip gpg yq brotli nodejs stow
RUN apt clean

COPY --chown=0:0 entrypoint.sh /
COPY --chown=0:0 .stow-local-ignore /home/tooling/
RUN \
    # add user and configure it
    useradd -u 10001 -G sudo,root -d /home/user --shell /bin/bash -m user && \
    # Setup $PS1 for a consistent and reasonable prompt
    touch /etc/profile.d/prompt.sh && \
    chown 10001 /etc/profile.d/prompt.sh && \
    echo "export PS1='\W \`git branch --show-current 2>/dev/null | sed -r -e \"s@^(.+)@\(\1\) @\"\`$ '" >> /etc/profile.d/prompt.sh && \
    # Copy the global git configuration to user config as global /etc/gitconfig
    #  file may be overwritten by a mounted file at runtime
    cp /etc/gitconfig ${HOME}/.gitconfig && \
    chown 10001 ${HOME}/ ${HOME}/.gitconfig ${HOME}/.stow-local-ignore && \
    # Set permissions on /etc/passwd and /home to allow arbitrary users to write
    chgrp -R 0 /home && \
    chmod -R g=u /etc/passwd /etc/group /home && \
    chmod +x /entrypoint.sh && \
    # Create symbolic links from /home/tooling/ -> /home/user/
    stow . -t /home/user/ -d /home/tooling/ && \
    # .viminfo cannot be a symbolic link for security reasons, so copy it to /home/user/
    cp /home/tooling/.viminfo /home/user/.viminfo && \
    # Bash-related files are backed up to /home/tooling/ incase they are deleted when persistUserHome is enabled.
    cp /home/user/.bashrc /home/tooling/.bashrc && \
    cp /home/user/.bash_profile /home/tooling/.bash_profile && \
    chown 10001 /home/tooling/.bashrc /home/tooling/.bash_profile

USER 10001
ENV VSCODE_NODEJS_RUNTIME_DIR=/usr/bin
ENV HOME=/home/user
WORKDIR /projects
ENTRYPOINT [ "/entrypoint.sh" ]
CMD ["tail", "-f", "/dev/null"]
