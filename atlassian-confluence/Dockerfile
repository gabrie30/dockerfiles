FROM zcalusic/debian-stretch as builder
MAINTAINER Zlatko Čalušić <zcalusic@bitsync.net>

ARG CONFLUENCE_VERSION

ENV JAVA_HOME /usr/java
ENV CONFLUENCE_VERSION $CONFLUENCE_VERSION
ENV CONFLUENCE_INSTALL_DIR /opt/atlassian/confluence

RUN mkdir "$JAVA_HOME" \
    && wget --dot-style=mega -O- --header "Cookie: oraclelicense=a" "http://download.oracle.com/otn-pub/java/jdk/8u181-b13/96a7b8442fe848ef90c96a2fad6ed6d1/server-jre-8u181-linux-x64.tar.gz" | tar xz --strip-components=1 -C "$JAVA_HOME" \
    && mkdir -p "$CONFLUENCE_INSTALL_DIR" \
    && wget --dot-style=mega -O- "https://downloads.atlassian.com/software/confluence/downloads/atlassian-confluence-${CONFLUENCE_VERSION}.tar.gz" | tar xz --strip-components=1 -C "$CONFLUENCE_INSTALL_DIR" \
    && sed -i -e 's/-Xms\([0-9]\+[kmg]\) -Xmx\([0-9]\+[kmg]\)/-Xms\${JVM_MINIMUM_MEMORY:=\1} -Xmx\${JVM_MAXIMUM_MEMORY:=\2} \${JVM_SUPPORT_RECOMMENDED_ARGS} -Dconfluence.home=\${CONFLUENCE_HOME}/g' "$CONFLUENCE_INSTALL_DIR/bin/setenv.sh" \
    && sed -i -e 's/port="8090"/port="8090" secure="${catalinaConnectorSecure}" scheme="${catalinaConnectorScheme}" proxyName="${catalinaConnectorProxyName}" proxyPort="${catalinaConnectorProxyPort}"/' "$CONFLUENCE_INSTALL_DIR/conf/server.xml"

FROM zcalusic/debian-stretch
MAINTAINER Zlatko Čalušić <zcalusic@bitsync.net>

ARG URL
ARG VCS_URL
ARG VCS_REF
ARG BUILD_DATE
ARG CONFLUENCE_VERSION

LABEL org.label-schema.name="Atlassian Confluence in Docker" \
      org.label-schema.description="Run Atlassian Confluence team collaboration software in a Docker container" \
      org.label-schema.vendor="Zlatko Čalušić" \
      org.label-schema.license="MIT" \
      org.label-schema.url=$URL \
      org.label-schema.vcs-url=$VCS_URL \
      org.label-schema.vcs-ref=$VCS_REF \
      org.label-schema.build-date=$BUILD_DATE \
      org.label-schema.schema-version="1.0"

ENV CONFLUENCE_VERSION $CONFLUENCE_VERSION
ENV CONFLUENCE_INSTALL_DIR /opt/atlassian/confluence
ENV CONFLUENCE_HOME /var/atlassian/application-data/confluence
ENV CONFLUENCE_USER daemon
ENV CONFLUENCE_GROUP daemon
ENV JAVA_HOME /usr/java
ENV PATH $JAVA_HOME/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin

COPY --from=builder $JAVA_HOME $JAVA_HOME
COPY --from=builder $CONFLUENCE_INSTALL_DIR $CONFLUENCE_INSTALL_DIR

RUN chown "$CONFLUENCE_USER:$CONFLUENCE_GROUP" "$CONFLUENCE_INSTALL_DIR/conf" "$CONFLUENCE_INSTALL_DIR/logs" "$CONFLUENCE_INSTALL_DIR/temp" "$CONFLUENCE_INSTALL_DIR/work"

EXPOSE 8090 8091

COPY entrypoint.sh /entrypoint.sh

CMD [ "/entrypoint.sh" ]
