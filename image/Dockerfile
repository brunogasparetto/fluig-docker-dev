FROM ubuntu:22.04

ARG FLUIG="2.0"
ARG TZ=UTC
ARG NODE=true
ARG SOLR=true
ARG LS_TYPE="ls"
ARG LS_SERVER="localhost"
ARG LS_PORT=5555

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
# Instala dependência do Fluig e lib para MySQL
    && apt-get -y install fontconfig \
    && apt-get clean \
    && dpkg -i /var/fluig/mysql-connector-j_8.1.0-1ubuntu22.04_all.deb \
# Configura o timezone
    && ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone \
# Configura o EntryPoint
    && mv /var/fluig/start-container /usr/local/bin/start-container \
    && sed -i 's/\r//' /usr/local/bin/start-container \
    && chmod +x /usr/local/bin/start-container \
# Indica o install.conf e alias de log de acordo com a versão do Fluig
    && if [ "$FLUIG" = "2.0" ]; then \
        mv /var/fluig/2.0/install.conf /var/fluig/install.conf; \
        echo "alias log='tail -f /opt/fluig/appserver/standalone/log/server.log'" >> /root/.bashrc; \
    else \
        mv /var/fluig/1.8/install.conf /var/fluig/install.conf; \
        echo "alias log='tail -f /opt/fluig/appserver/domain/servers/fluig1/log/server.log'" >> /root/.bashrc; \
    fi \
# Trata a não instalação do RealTime
    && if [ "$NODE" = "false" ]; then \
        if [ "$FLUIG" = "2.0" ]; then \
            sed -i 's/service fluig_RealTime --full-restart/# service fluig_RealTime --full-restart/g' /usr/local/bin/start-container; \
            sed -i 's/resource_node_install=true/resource_node_install=false/g' /var/fluig/install.conf; \
            sed -i 's/node_enabled=true/node_enabled=false/g' /var/fluig/install.conf; \
        else \
            sed -i 's/service fluig_RealTime --full-restart/# service fluig_RealTime --full-restart/g' /usr/local/bin/start-container; \
            sed -i 's/install.node=true/install.node=false/g' /var/fluig/install.conf; \
        fi \
    fi \
# Trata a não instalação do Indexador
    && if [ "$SOLR" = "false" ]; then \
        if [ "$FLUIG" = "2.0" ]; then \
            sed -i 's/service fluig_Indexer --full-restart/# service fluig_Indexer --full-restart/g' /usr/local/bin/start-container; \
            sed -i 's/resource_solr_install=true/resource_solr_install=false/g' /var/fluig/install.conf; \
        else \
            sed -i 's/service fluig_Indexer --full-restart/# service fluig_Indexer --full-restart/g' /usr/local/bin/start-container; \
            sed -i 's/install.solr=true/install.solr=false/g' /var/fluig/install.conf; \
        fi \
    fi \
# Configura o LS
    && if [ "$FLUIG" = "2.0" ]; then \
        sed -i "s|license_type=ls|license_type=$LS_TYPE|g" /var/fluig/install.conf; \
        sed -i "s|ls_server=localhost|ls_server=$LS_SERVER|g" /var/fluig/install.conf; \
        sed -i "s|ls_port=5555|ls_port=$LS_PORT|g" /var/fluig/install.conf; \
    else \
        sed -i "s|license.type=ls|license.type=$LS_TYPE|g" /var/fluig/install.conf; \
        sed -i "s|ls.server=localhost|ls.server=$LS_SERVER|g" /var/fluig/install.conf; \
        sed -i "s|ls.port=5555|ls.port=$LS_PORT|g" /var/fluig/install.conf; \
    fi \
# Executa a instalação do Fluig
    && cd /var/fluig/installer \
    && chmod +x ./jdk-64/bin/java \
    && ./jdk-64/bin/java -cp fluig-installer.jar com.fluig.install.ExecuteInstall /var/fluig/install.conf \
# Parametriza os endereços do Fluig de acordo com a versão
    && if [ "$FLUIG" = "2.0" ]; then \
        sed -i 's#<inet-address .*#<any-address/>#g' /opt/fluig/appserver/standalone/configuration/standalone.xml; \
    else \
        mv /var/fluig/1.8/domain.xml /var/fluig/1.8/host.xml /opt/fluig/appserver/domain/configuration/; \
    fi \
# Remove a pasta da instalação
    && cd / \
    && rm -fr /var/fluig \
    && true
