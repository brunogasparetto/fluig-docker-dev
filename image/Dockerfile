FROM ubuntu:22.04

ENV DEBIAN_FRONTEND noninteractive
ENV TZ=UTC

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

COPY start-container /usr/local/bin/start-container

RUN apt-get update \
    && apt-get upgrade -y \
    && apt-get -y install fontconfig \
    && apt-get clean \
    && echo "alias log='tail -f /opt/fluig/appserver/domain/servers/fluig1/log/server.log'" > /root/.bash_aliases \
    && chmod +x /usr/local/bin/start-container

COPY . /var/fluig

RUN dpkg -i /var/fluig/mysql-connector-j_8.1.0-1ubuntu22.04_all.deb

EXPOSE 8080 7777 8888 8000 8983 9990

ENTRYPOINT ["start-container"]

WORKDIR /var/fluig/installer

RUN ./jdk-64/bin/java -cp fluig-installer.jar com.fluig.install.ExecuteInstall /var/fluig/install.conf

RUN mv /var/fluig/domain.xml /var/fluig/host.xml /opt/fluig/appserver/domain/configuration/

RUN rm -fr /var/fluig

WORKDIR /opt/fluig
