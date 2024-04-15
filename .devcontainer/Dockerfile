FROM public.ecr.aws/ubuntu/ubuntu:22.04

ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --no-install-recommends \
    apt-transport-https \
    build-essential \
    ca-certificates \
    curl \
    git \
    gnupg-agent \
    groff \
    iproute2 \
    iptables \
    jq \
    less \
    libarchive-tools \
    make \
    mandoc \
    python-is-python3 \
    python3-apt \
    python3-pip \
    python3-psutil \
    python3-venv \
    shellcheck \
    software-properties-common \
    sudo \
    unzip \
    vim \
    wget && \
    apt-get clean

RUN useradd -u 1000 -s /bin/bash -m dev && \
    usermod -aG sudo dev && \
    sed -i '/%sudo/a%sudo ALL=(ALL) NOPASSWD:ALL' /etc/sudoers && \
    install -d -m 0750 -o dev /home/dev/.local /home/dev/.local/bin

USER dev

ENV BIN_DIR=/home/dev/.local/bin
ENV \
    AWSCLI_VER=2.15.36 \
    CRANE_VER=0.19.1 \
    HELM_VER=3.14.3 \
    K9S_VER=0.32.4 \
    KUBECTL_VER=1.29.2 \
    PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin:${BIN_DIR}" \
    TERRAFORM_VER=1.7.5 \
    TF_PLUGIN_CACHE_DIR="/home/dev/.terraform.d/plugin-cache" \
    TFLINT_VER=0.50.3

RUN python -m pip install --user \
    ansible \
    ansible-lint \
    black \
    flake8 \
    isort

# aws
RUN if [ "$(uname -i)" = "x86_64" ]; then AWSCLI_ARCH="x86_64"; else AWSCLI_ARCH="aarch64"; fi && \
    curl -fLs -o - "https://awscli.amazonaws.com/awscli-exe-linux-${AWSCLI_ARCH}-${AWSCLI_VER}.zip" | bsdtar -xf - -C ${BIN_DIR}/../ && \
    chmod +x ${BIN_DIR}/../aws/install && \
    ${BIN_DIR}/../aws/install --bin-dir ${BIN_DIR} --install-dir ${BIN_DIR}/../aws/local/aws-cli --update && \
    chmod +x ${BIN_DIR}/../aws/local/aws-cli/v2/dist/aws

# ssm
RUN if [ "$(uname -i)" = "x86_64" ]; then AWSSSM_ARCH="64bit"; else AWSSSM_ARCH="arm64"; fi && \
    curl -fLs -o /tmp/session-manager-plugin.deb \
        "https://s3.amazonaws.com/session-manager-downloads/plugin/latest/ubuntu_${AWSSSM_ARCH}/session-manager-plugin.deb" && \
    sudo dpkg -i /tmp/session-manager-plugin.deb

# crane
RUN if [ "$(uname -i)" = "x86_64" ]; then CRANE_ARCH="x86_64"; else CRANE_ARCH="arm64"; fi && \
    curl -fLs -o - \
        "https://github.com/google/go-containerregistry/releases/download/v${CRANE_VER}/go-containerregistry_Linux_${CRANE_ARCH}.tar.gz" | \
        tar -xz -C ${BIN_DIR}/ -f - crane

# helm
RUN if [ "$(uname -i)" = "x86_64" ]; then HELM_ARCH="amd64"; else HELM_ARCH="arm64"; fi && \
    curl -fLs -o - \
        "https://get.helm.sh/helm-v${HELM_VER}-linux-${HELM_ARCH}.tar.gz" | \
        tar -xz -C ${BIN_DIR}/ --strip-components=1 -f - linux-${HELM_ARCH}/helm

# k9s
RUN if [ "$(uname -i)" = "x86_64" ]; then K9S_ARCH="amd64"; else K9S_ARCH="arm64"; fi && \
    curl -fLs -o - \
        "https://github.com/derailed/k9s/releases/download/v${K9S_VER}/k9s_Linux_${K9S_ARCH}.tar.gz" | \
        tar -xz -C ${BIN_DIR}/ -f - k9s && \
    chmod +x ${BIN_DIR}/k9s

# kubectl
RUN if [ "$(uname -i)" = "x86_64" ]; then KUBECTL_ARCH="amd64"; else KUBECTL_ARCH="arm64"; fi && \
    curl -fLs -o ${BIN_DIR}/kubectl \
        "https://dl.k8s.io/release/v${KUBECTL_VER}/bin/linux/${KUBECTL_ARCH}/kubectl" && \
    chmod +x ${BIN_DIR}/kubectl

# terraform
RUN if [ "$(uname -i)" = "x86_64" ]; then TERRAFORM_ARCH="amd64"; else TERRAFORM_ARCH="arm64"; fi && \
    curl -fLs -o - \
        "https://releases.hashicorp.com/terraform/${TERRAFORM_VER}/terraform_${TERRAFORM_VER}_linux_${TERRAFORM_ARCH}.zip" | \
        bsdtar -xf - -C ${BIN_DIR}/ && \
    chmod +x ${BIN_DIR}/terraform

# tflint
RUN if [ "$(uname -i)" = "x86_64" ]; then TFLINT_ARCH="amd64"; else TFLINT_ARCH="arm64"; fi && \
    curl -fLs -o - \
        "https://github.com/terraform-linters/tflint/releases/download/v${TFLINT_VER}/tflint_linux_${TFLINT_ARCH}.zip" | \
        bsdtar -xf - -C ${BIN_DIR}/ && \
    chmod +x ${BIN_DIR}/tflint
