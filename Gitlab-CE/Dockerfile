FROM public.ecr.aws/amazonlinux/amazonlinux:2023
LABEL org.opencontainers.image.authors="mshah.omar@gmail.com"

RUN dnf update -y
RUN dnf install -y policycoreutils-python-utils openssh-server openssh-clients perl

RUN curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.rpm.sh | bash

RUN EXTERNAL_URL="https://gitlab-dev.shahomar.net" dnf install -y gitlab-ce
