#FROM public.ecr.aws/amazonlinux/amazonlinux:2023
FROM public.ecr.aws/amazonlinux/amazonlinux:2023.0.20230322.0
LABEL org.opencontainers.image.authors="mshah.omar@gmail.com"

RUN dnf update -y
RUN dnf install -y java-17-amazon-corretto-devel \
                    glibc-locale-source glibc-langpack-en gzip \
                    shadow-utils.x86_64 unzip dejavu-fonts-all.noarch

#RUN dnf install -y java-11-amazon-corretto-headless \

WORKDIR /usr/share/i18n/charmaps
RUN gzip -dk UTF-8.gz
#RUN dnf install -y policycoreutils-python-utils openssh-server openssh-clients perl perl-lib

RUN localedef -i en_US -f UTF-8 en_US.UTF-8
RUN echo "LANG=en_US.utf-8" >> /etc/environment ; \
    echo "LANGUAGE=en_US:en" >> /etc/environment ; \
    echo "LC_ALL=en_US.utf-8" >> /etc/environment

ENV LANG='en_US.UTF-8' \
    LANGUAGE='en_US:en' \
    LC_ALL='en_US.UTF-8'

#
# SonarQube setup
# Refer: https://github.com/SonarSource/docker-sonarqube/tree/master/10/community
#
ARG SONARQUBE_VERSION=10.2.1.78527
ARG SONARQUBE_ZIP_URL=https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-${SONARQUBE_VERSION}.zip
#ENV JAVA_HOME='/usr/lib/jvm/java-11-amazon-corretto.x86_64/bin' \
ENV DOCKER_RUNNING="true" \
    JAVA_HOME='/usr/lib/jvm/java-17-amazon-corretto.x86_64' \
    SONARQUBE_HOME=/opt/sonarqube \
    SONAR_VERSION="${SONARQUBE_VERSION}" \
    SQ_DATA_DIR="/opt/sonarqube/data" \
    SQ_EXTENSIONS_DIR="/opt/sonarqube/extensions" \
    SQ_LOGS_DIR="/opt/sonarqube/logs" \
    SQ_TEMP_DIR="/opt/sonarqube/temp"

# To fix error "gpg: keyserver receive failed: No dirmngr" 
RUN dnf swap gnupg2-minimal gnupg2-full -y 

# Resume Sonarqube setup
RUN set -eux; \
    groupadd --system --gid 1000 sonarqube; \
    useradd --system --uid 1000 --gid sonarqube sonarqube; \
    echo "networkaddress.cache.ttl=5" >> "${JAVA_HOME}/conf/security/java.security"; \
    sed --in-place --expression="s?securerandom.source=file:/dev/random?securerandom.source=file:/dev/urandom?g" "${JAVA_HOME}/conf/security/java.security"; \
    # pub   2048R/D26468DE 2015-05-25
    #       Key fingerprint = F118 2E81 C792 9289 21DB  CAB4 CFCA 4A29 D264 68DE
    # uid                  sonarsource_deployer (Sonarsource Deployer) <infra@sonarsource.com>
    # sub   2048R/06855C1D 2015-05-25
    for server in $(shuf -e hkps://keys.openpgp.org \
                            hkps://keyserver.ubuntu.com) ; do \
        gpg --batch --keyserver "${server}" --recv-keys 679F1EE92B19609DE816FDE81DB198F93525EC1A && break || : ; \
    done; \
    mkdir --parents /opt; \
    cd /opt; \
    curl --fail --location --output sonarqube.zip --silent --show-error "${SONARQUBE_ZIP_URL}"; \
    curl --fail --location --output sonarqube.zip.asc --silent --show-error "${SONARQUBE_ZIP_URL}.asc"; \
    gpg --batch --verify sonarqube.zip.asc sonarqube.zip; \
    unzip -q sonarqube.zip; \
    mv "sonarqube-${SONARQUBE_VERSION}" sonarqube; \
    rm sonarqube.zip*; \
    rm -rf ${SONARQUBE_HOME}/bin/*; \
    ln -s "${SONARQUBE_HOME}/lib/sonar-application-${SONARQUBE_VERSION}.jar" "${SONARQUBE_HOME}/lib/sonarqube.jar"; \
    chmod -R 555 ${SONARQUBE_HOME}; \
    chmod -R ugo+wrX "${SQ_DATA_DIR}" "${SQ_EXTENSIONS_DIR}" "${SQ_LOGS_DIR}" "${SQ_TEMP_DIR}"

COPY entrypoint.sh ${SONARQUBE_HOME}/docker/

WORKDIR ${SONARQUBE_HOME}
EXPOSE 9000

RUN chmod +x ${SONARQUBE_HOME}/docker/entrypoint.sh

USER sonarqube
STOPSIGNAL SIGINT

ENTRYPOINT ["/opt/sonarqube/docker/entrypoint.sh"]

#WORKDIR /
