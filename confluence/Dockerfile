FROM java:8
MAINTAINER Jasper Solutions

# Setup useful environment variables
ENV CONFLUENCE_HOME     /var/atlassian/application-data/confluence
ENV CONFLUENCE_INSTALL  /opt/atlassian/confluence
ENV CONF_VERSION  6.0.5

LABEL Description="This image is used to start Atlassian Confluence" Vendor="Atlassian" Version="${CONF_VERSION}"

ENV CONFLUENCE_DOWNLOAD_URL http://www.atlassian.com/software/confluence/downloads/binary/atlassian-confluence-${CONF_VERSION}.tar.gz

# Use the default unprivileged account. This could be considered bad practice
# on systems where multiple processes end up being executed by 'daemon' but
# here we only ever run one process anyway.
#ENV RUN_USER            daemon
#ENV RUN_GROUP           daemon

# Install Atlassian Confluence and helper tools and setup initial home
# directory structure.
RUN set -x \
    && apt-get update --quiet \
    && apt-get install --quiet --yes --no-install-recommends libtcnative-1 xmlstarlet \
    && apt-get clean \
    && mkdir -p                           "${CONFLUENCE_HOME}" \
    && chmod -R 700                       "${CONFLUENCE_HOME}" \
    && chown daemon:daemon                "${CONFLUENCE_HOME}" \
    && mkdir -p                           "${CONFLUENCE_INSTALL}/conf" \
    && curl -Ls                           "${CONFLUENCE_DOWNLOAD_URL}" | tar -xz --directory "${CONFLUENCE_INSTALL}" --strip-components=1 --no-same-owner \
    && curl -Ls                           "https://jdbc.postgresql.org/download/postgresql-9.4.1212.jar" -o "${JIRA_INSTALL}/lib/postgresql-9.4.1212.jar" \   
    && chmod -R 700                       "${CONFLUENCE_INSTALL}/conf" \
    && chmod -R 700                       "${CONFLUENCE_INSTALL}/temp" \
    && chmod -R 700                       "${CONFLUENCE_INSTALL}/logs" \
    && chmod -R 700                       "${CONFLUENCE_INSTALL}/work" \
    && chown -R daemon:daemon             "${CONFLUENCE_INSTALL}/conf" \
    && chown -R daemon:daemon             "${CONFLUENCE_INSTALL}/temp" \
    && chown -R daemon:daemon             "${CONFLUENCE_INSTALL}/logs" \
    && chown -R daemon:daemon             "${CONFLUENCE_INSTALL}/work" \
    && echo -e                            "\nconfluence.home=${CONFLUENCE_HOME}" >> "${CONFLUENCE_INSTALL}/confluence/WEB-INF/classes/confluence-init.properties" \
    && xmlstarlet                         ed --inplace \
        --delete                          "Server/@debug" \
        --delete                          "Server/Service/Connector/@debug" \
        --delete                          "Server/Service/Connector/@useURIValidationHack" \
        --delete                          "Server/Service/Connector/@minProcessors" \
        --delete                          "Server/Service/Connector/@maxProcessors" \
        --delete                          "Server/Service/Engine/@debug" \
        --delete                          "Server/Service/Engine/Host/@debug" \
        --delete                          "Server/Service/Engine/Host/Context/@debug" \
                                          "${CONFLUENCE_INSTALL}/conf/server.xml" \
    && touch -d "@0"                      "${CONFLUENCE_INSTALL}/conf/server.xml"

# Use the default unprivileged account. This could be considered bad practice
# on systems where multiple processes end up being executed by 'daemon' but
# here we only ever run one process anyway.
USER daemon:daemon

# Expose default HTTP connector port.
EXPOSE 8090
EXPOSE 8091

# Set volume mount points for installation and home directory. Changes to the
# home directory needs to be persisted as well as parts of the installation
# directory due to eg. logs.
VOLUME ["${CONFLUENCE_INSTALL}", "${CONFLUENCE_HOME}"]

# Set the default working directory as the Confluence installation directory.
WORKDIR ${CONFLUENCE_INSTALL}

# Run Atlassian Confluence as a foreground process by default.
CMD ["./bin/catalina.sh", "run"]
