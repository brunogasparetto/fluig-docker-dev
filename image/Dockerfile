FROM ubuntu:22.04

ARG TZ=UTC
ARG NODE=true
ARG SOLR=true

ENV DEBIAN_FRONTEND=noninteractive

WORKDIR /opt/fluig

EXPOSE 8080 7777 8888 8000 8983 9990

ENTRYPOINT ["start-container"]

COPY . /var/fluig

RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get -y install fontconfig \
    && apt-get clean \
    && mv /var/fluig/start-container /usr/local/bin/start-container \
    && chmod +x /usr/local/bin/start-container \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    && echo "alias log='tail -f /opt/fluig/appserver/domain/servers/fluig1/log/server.log'" >> /root/.bashrc \
    && dpkg -i /var/fluig/mysql-connector-j_8.1.0-1ubuntu22.04_all.deb \
    && cd /var/fluig/installer \
    && chmod +x ./jdk-64/bin/java \
    && if [ "$NODE" = "false" ]; then \
        sed -i 's/service fluig_RealTime --full-restart/# service fluig_RealTime --full-restart/g' /usr/local/bin/start-container; \
        sed -i 's/install.node=true/install.node=false/g' /var/fluig/install.conf; \
    fi \
    && if [ "$SOLR" = "false" ]; then \
        sed -i 's/service fluig_Indexer --full-restart/# service fluig_Indexer --full-restart/g' /usr/local/bin/start-container; \
        sed -i 's/install.solr=true/install.solr=false/g' /var/fluig/install.conf; \
    fi \
    && ./jdk-64/bin/java -cp fluig-installer.jar com.fluig.install.ExecuteInstall /var/fluig/install.conf \
    && mv /var/fluig/domain.xml /var/fluig/host.xml /opt/fluig/appserver/domain/configuration/ \
    && cd / \
    && rm -fr /var/fluig \
    && true
