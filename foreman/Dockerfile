FROM quay.io/devfile/base-developer-image:ubi8-latest
LABEL maintainer="Jason Galens"

LABEL name="devfile/foreman-developer-image"
LABEL version="ubi9"

ARG RUBY_VERSION="2.7"
ARG NODEJS_VERSION="12"
ARG FOREMAN_REF=3.3-stable

USER 0

RUN \
  dnf -y module enable ruby:2.7 nodejs:12; \
  dnf -y install postgresql-libs ruby{,gems} rubygem-{rake,bundler} npm nc hostname redhat-rpm-config git \
    gcc-c++ make bzip2 gettext tar libxml2-devel libcurl-devel ruby-devel postgresql-devel; \
  rpm --import https://dl.fedoraproject.org/pub/epel/RPM-GPG-KEY-EPEL-8; \
  rpm -i https://rpmfind.net/linux/almalinux/8.10/AppStream/x86_64/os/Packages/postgresql-13.20-1.module_el8.10.0+3967+334cd65a.x86_64.rpm; \
  rpm -i https://dl.fedoraproject.org/pub/epel/8/Everything/x86_64/Packages/q/qpid-proton-c-0.37.0-1.el8.x86_64.rpm; \
  rpm -i https://dl.fedoraproject.org/pub/epel/8/Everything/x86_64/Packages/q/qpid-proton-c-devel-0.37.0-1.el8.x86_64.rpm; \
  dnf clean all

ENV FOREMAN_FQDN=foreman.example.com
ENV FOREMAN_DOMAIN=example.com
ENV RAILS_ENV=production
ENV FOREMAN_APIPIE_LANGS=en
ENV BUNDLER_SKIPPED_GROUPS="test development openid libvirt journald facter console"
ENV DATABASE_URL=nulldb://nohost

ARG HOME=/home/foreman
USER 10001
WORKDIR $HOME

RUN git clone --branch=${FOREMAN_REF} --single-branch https://github.com/theforeman/foreman.git ${HOME}

# Adding missing gems, for tzdata see https://bugzilla.redhat.com/show_bug.cgi?id=1611117
RUN echo gem '"tzinfo-data"' > bundler.d/container.rb
RUN \
  bundle config set --local without "${BUNDLER_SKIPPED_GROUPS}"; \
  bundle config set --local clean true; \
  bundle config set --local path vendor; \
  bundle config set --local jobs 5; \
  bundle config set --local retry 3; \
  bundle install; \
  bundle binstubs --all; \
  rm -rf vendor/ruby/*/cache/*.gem; \
  find vendor/ruby/*/gems -name "*.c" -delete; \
  find vendor/ruby/*/gems -name "*.o" -delete
RUN \
  make -C locale all-mo; \
  mv -v db/schema.rb.nulldb db/schema.rb; \
  bundle exec rake assets:clean assets:precompile apipie:cache:index
RUN \
  npm install --no-optional; \
  ./node_modules/webpack/bin/webpack.js --config config/webpack.config.js; \
  npm run analyze

USER 0

# Set permissions on /etc/passwd, /etc/group, /etc/pki and /home to allow arbitrary users to write
RUN \
  chgrp -R 0 /home; \
  chmod -R g=u /etc/passwd /etc/group /home /etc/pki

# cleanup dnf cache
RUN dnf -y clean all --enablerepo='*'

USER 10001

ENV HOME=/home/user
