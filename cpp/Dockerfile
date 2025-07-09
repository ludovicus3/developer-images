FROM quay.io/ludovicus/base-developer-image:stream9
LABEL maintainer="Jason Galens"

LABEL name="devfile/cpp-developer-image"
LABEL version="stream9"

USER 0

# $PROFILE_EXT contains all additions made to the bash environment
ENV PROFILE_EXT=/etc/profile.d/udi_environment.sh
RUN \
  touch ${PROFILE_EXT}; \
  chown 10001 ${PROFILE_EXT}

RUN \
  dnf config-manager --set-enabled crb; \
  dnf install -y cmake make ninja-build gcc-toolset-14{,-libasan-devel,-libatomic-devel,-libitm-devel,-liblsan-devel,-libtsan-devel,-libubsan-devel,-offload-nvptx} clang clangd clang-tools-extra ccache vulkan-devel SDL2-devel SDL2_gfx-devel SDL2_net-devel SDL2_image-devel SDL2_mixer-devel SDL2_ttf-devel mesa-libGL-devel alsa-lib-devel pulseaudio-libs-devel dbus-devel wayland-devel libX11-devel libXcursor-devel libXrandr-devel libXfixes-devel libXi-devel libXrender-devel libusb-devel; \
  dnf clean all -y --enablerepo='*'

RUN echo "source /opt/rh/gcc-toolset-14/enable" >> ${PROFILE_EXT}

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

