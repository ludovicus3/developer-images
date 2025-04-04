ARG DEBIAN_VERSION=bookworm-slim
FROM debian:${DEBIAN_VERSION}

LABEL name="ludovicus/base-development-image"
LABEL version=${DEBIAN_VERSION}

#labels for container catalog
LABEL summary="devfile base developer image"
LABEL description="Image with base developers tools. Languages SDK and runtimes excluded."
LABEL io.k8s.display-name="devfile-developer-base"

USER 0

RUN \
  ln -s bash /bin/sh.bash; \
  mv /bin/sh.bash /bin/sh

RUN \
  set -eux; \
  apt-get update; \
  apt-get install -y --no-install-recommends bash curl diffutils git git-lfs iproute2 jq less lsof man nano \
            procps p7zip-full libdigest-sha-perl net-tools openssh-client rsync \
            socat sudo time vim wget zip gpg yq brotli stow ca-certificates; \
  rm -rf /var/lib/apt/lists/*

## podman buildah skopeo
RUN \
  set -eux; \
  apt-get update; \
  apt-get install -y --no-install-recommends podman buildah skopeo fuse-overlayfs; \
  rm -rf /var/lib/apt/lists/*

## gh-cli ripgrep bat fd
RUN \
  set -eux; \
  apt-get update; \
  apt-get install -y --no-install-recommends gh ripgrep bat fd-find; \
  rm -rf /var/lib/apt/lists/*

ENV HOME=/home/tooling
RUN mkdir -p /home/tooling/
COPY --chown=0:0 entrypoint.sh /
COPY --chown=0:0 .stow-local-ignore /home/tooling/

# Define user directory for binaries
ENV PATH="/home/user/.local/bin:$PATH"

# Set up environment variables to note that this is
# not starting with usernamespace and default to
# isolate the filesystem with chroot.
ENV _BUILDAH_STARTED_IN_USERNS="" BUILDAH_ISOLATION=chroot

# Tweaks to make rootless buildah work
RUN \
  touch /etc/subgid /etc/subuid; \
  chmod g=u /etc/subgid /etc/subuid /etc/passwd; \
  echo user:10000:65536 > /etc/subuid; \
  echo user:10000:65536 > /etc/subgid

# Adjust storage.conf to enable Fuse storage.
COPY --chown=0:0 storage.conf /etc/containers/storage.conf
#RUN sed -i -e 's|^#mount_program|mount_program|g' -e '/additionalimage.*/a "/var/lib/shared",' /etc/containers/storage.conf
RUN \
  mkdir -p /var/lib/shared/overlay-images /var/lib/shared/overlay-layers; \
  touch /var/lib/shared/overlay-images/images.lock; \
  touch /var/lib/shared/overlay-layers/layers.lock

RUN \
  set -eux; \
  # add user and configure it
  useradd -u 10001 -G sudo,root -d /home/user --shell /bin/bash -m user; \
  # Setup $PS1 for a consistent and reasonable prompt
  touch /etc/profile.d/prompt.sh; \
  chown 10001 /etc/profile.d/prompt.sh; \
  echo "export PS1='\W \`git branch --show-current 2>/dev/null | sed -r -e \"s@^(.+)@\(\1\) @\"\`$ '" >> /etc/profile.d/prompt.sh; \
  # Copy the global git configuration to user config as global /etc/gitconfig
  #  file may be overwritten by a mounted file at runtime
  cp /etc/gitconfig ${HOME}/.gitconfig; \
  chown 10001 ${HOME}/ ${HOME}/.gitconfig ${HOME}/.stow-local-ignore; \
  # Set permissions on /etc/passwd and /home to allow arbitrary users to write
  chgrp -R 0 /home; \
  chmod -R g=u /etc/passwd /etc/group /home; \
  chmod +x /entrypoint.sh; \
  # Create symbolic links from /home/tooling/ -> /home/user/
  stow . -t /home/user/ -d /home/tooling/; \
  # Bash-related files are backed up to /home/tooling/ incase they are deleted when persistUserHome is enabled.
  cp /home/user/.bashrc /home/tooling/.bashrc; \
  chown 10001 /home/tooling/.bashrc

USER 10001

ENV VSCODE_NODEJS_RUNTIME_DIR=/checode/checode-linux-musl

ENV HOME=/home/user
WORKDIR /projects
ENTRYPOINT [ "/entrypoint.sh" ]
CMD ["tail", "-f", "/dev/null"]
