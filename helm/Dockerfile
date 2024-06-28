FROM quay.io/ludovicus/base-developer-image:ubi8-latest

LABEL name="devfile/helm-developer-image"
LABEL version="ubi8"

#labels for container catalog
LABEL summary="devfile helm developer image"
LABEL description="Image with developers tools. Languages SDK and runtimes included."
LABEL io.k8s.display-name="devfile-developer-helm"

USER 0

## helm
RUN <<EOF
set -euf -o pipefail
TEMP_DIR="$(mktemp -d)"
cd "${TEMP_DIR}"
HELM_VERSION="3.15.2"
HELM_ARCH="linux-amd64"
HELM_TGZ="helm-v${HELM_VERSION}-${HELM_ARCH}.tar.gz"
HELM_TGZ_URL="https://get.helm.sh/${HELM_TGZ}"
curl -sSLO "${HELM_TGZ_URL}"
curl -sSLO "${HELM_TGZ_URL}.sha256sum"
sha256sum -c "${HELM_TGZ}.sha256sum" 2>&1 | grep OK
tar -zxv --no-same-owner -f "${HELM_TGZ}"
mv "${HELM_ARCH}"/helm /usr/local/bin/helm
cd -
rm -rf "${TEMP_DIR}"
EOF

USER 10001
