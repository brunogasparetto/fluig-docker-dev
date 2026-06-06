FROM ubuntu:22.04

ARG TZ=UTC
ARG NODE=true
ARG SOLR=true

ENV DEBIAN_FRONTEND=noninteractive
ENV FLUIG_CONFIG_SERVER_ADDRESS_PUBLIC=0.0.0.0
ENV FLUIG_CONFIG_SERVER_ADDRESS_MANAGEMENT=127.0.0.1
ENV FLUIG_CONFIG_SERVER_ADDRESS_UNSECURE=0.0.0.0

WORKDIR /opt/fluig

EXPOSE 8080 7777 8888 8000 8983 9990

ENTRYPOINT ["start-container"]

COPY . /var/fluig

RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get -y install fontconfig \
    && apt-get clean \
    && mv /var/fluig/start-container /usr/local/bin/start-container \
    && sed -i 's/\r//' /usr/local/bin/start-container \
    && chmod +x /usr/local/bin/start-container \
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
    && echo "alias log='tail -f /opt/fluig/appserver/standalone/log/server.log'" >> /root/.bashrc \
    && dpkg -i /var/fluig/mysql-connector-j_8.1.0-1ubuntu22.04_all.deb \
    && cd /var/fluig/installer \
    && chmod +x ./jdk-64/bin/java \
    && if [ "$NODE" = "false" ]; then \
        sed -i 's/service fluig_RealTime --full-restart/# service fluig_RealTime --full-restart/g' /usr/local/bin/start-container; \
        sed -i 's/resource_node_install=true/resource_node_install=false/g' /var/fluig/install.conf; \
        sed -i 's/node_enabled=true/node_enabled=false/g' /var/fluig/install.conf; \
    fi \
    && if [ "$SOLR" = "false" ]; then \
        sed -i 's/service fluig_Indexer --full-restart/# service fluig_Indexer --full-restart/g' /usr/local/bin/start-container; \
        sed -i 's/resource_solr_install=true/resource_solr_install=false/g' /var/fluig/install.conf; \
    fi \
    && ./jdk-64/bin/java -cp fluig-installer.jar com.fluig.install.ExecuteInstall /var/fluig/install.conf \
    && cd / \
    && rm -fr /var/fluig \
    && true
