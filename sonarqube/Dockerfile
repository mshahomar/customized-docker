# Using AL2023, with Amazon-Corretto for Java version 17
FROM public.ecr.aws/amazonlinux/amazonlinux:2023.0.20230322.0
LABEL org.opencontainers.image.authors="mshah.omar@gmail.com"

RUN dnf update -y
RUN dnf install -y java-17-amazon-corretto-devel \
                    glibc-locale-source glibc-langpack-en gzip \
                    shadow-utils.x86_64 unzip dejavu-fonts-all.noarch

WORKDIR /usr/share/i18n/charmaps
RUN gzip -dk UTF-8.gz

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

# To fix CVE
RUN dnf upgrade -y --releasever=latest && dnf -y update
# CVE-2023-37920, CVE-2023-38545, CVE-2023-38546, CVE-2023-27533, CVE-2023-27534, CVE-2023-27535, CVE-2023-27536,
# CVE-2023-27537, CVE-2023-27538, CVE-2023-28319, CVE-2023-28321, CVE-2023-28322, CVE-2023-34969, CVE-2022-48554,
# CVE-2022-27405, CVE-2022-27406, CVE-2023-4156, CVE-2023-24593, CVE-2023-25180, CVE-2023-29499, CVE-2023-32611, 
# CVE-2023-32665, CVE-2023-4911, CVE-2023-4527, CVE-2023-4806, CVE-2023-4813, CVE-2023-21930, CVE-2023-21937, 
# CVE-2023-21938, CVE-2023-21939, CVE-2023-21954, CVE-2023-21967, CVE-2023-21968, CVE-2022-33068, CVE-2023-25193,
# CVE-2023-22006, CVE-2023-22036, CVE-2023-22041, CVE-2023-22043, CVE-2023-22044, CVE-2023-22045, CVE-2023-22049, 
# CVE-2023-25193, CVE-2023-22025, CVE-2023-22081, CVE-2022-40897, CVE-2022-41409, CVE-2023-2975, CVE-2023-3446, 
# CVE-2023-3817, CVE-2023-2650, CVE-2023-0464, CVE-2023-0465, CVE-2023-0466, CVE-2023-1255, CVE-2023-2953, CVE-2021-27212,
# CVE-2023-29491, CVE-2023-39615, CVE-2023-28484, CVE-2023-29469, CVE-2023-4039
# RUN dnf update -y ca-certificates --releasever 2023.1.20230823 ; \ 
#     dnf update -y curl --releasever 2023.2.20231011 ; \ 
#     dnf update -y curl --releasever 2023.0.20230607 ; \
#     dnf update -y curl --releasever 2023.1.20230725 ; \
#     dnf update -y dbus --releasever 2023.1.20230628 ; \
#     dnf update -y file --releasever 2023.1.20230906 ; \
#     dnf update -y freetype --releasever 2023.0.20230607 ; \
#     dnf update -y gawk --releasever 2023.1.20230823 ; \
#     dnf update -y glib2 --releasever 2023.1.20230628 ; \ 
#     dnf update -y glibc --releasever 2023.2.20231002 ; \
#     dnf update -y gnutls --releasever 2023.0.20230503; \
#     dnf update -y java-17-amazon-corretto --releasever 2023.0.20230503 ; \
#     dnf update -y harfbuzz --releasever=2023.0.20230222 ; \
#     dnf update -y java-17-amazon-corretto --releasever 2023.2.20231018 ; \
#     dnf update -y python-setuptools --releasever 2023.1.202307192023.1.20230906 ; \
#     dnf update -y python3.9 --releasever 2023.1.20230906 ; \
#     dnf update -y pcre2 --releasever 2023.1.20230809 ; \
#     dnf update -y openssl --releasever 2023.0.20230517 ; \
#     dnf update -y openssl --releasever 2023.1.20230628 ; \
#     dnf update -y openssl --releasever 2023.1.20230823 ; \
#     dnf update -y openldap --releasever 2023.0.20230503 ; \
#     dnf update -y openldap --releasever 2023.1.20230628 ; \
#     dnf update -y ncurses --releasever 2023.1.20230628 ; \
#     dnf update -y zstd --releasever 2023.1.20230719 ; \
#     dnf update -y libxml2 --releasever 2023.0.20230503 ; \
#     dnf update -y libxml2 --releasever 2023.2.20230920 ; \
#     dnf update -y gcc --releasever 2023.1.20230912 ; \
#     dnf update -y dotnet6.0 --releasever 2023.2.20231016

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
