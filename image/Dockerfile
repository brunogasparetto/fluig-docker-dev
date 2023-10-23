FROM ubuntu:22.04

WORKDIR /var/fluig

ENV DEBIAN_FRONTEND noninteractive
ENV TZ=UTC

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

RUN apt-get update \
    && apt -y install fontconfig

COPY installer /var/fluig

COPY install.conf /var/fluig/

# Instalar o driver JDBC para MySQL 8
COPY mysql-connector-j_8.1.0-1ubuntu22.04_all.deb /var/fluig/
RUN dpkg -i mysql-connector-j_8.1.0-1ubuntu22.04_all.deb

RUN ./jdk-64/bin/java -cp fluig-installer.jar com.fluig.install.ExecuteInstall

WORKDIR /opt/fluig

COPY domain.xml appserver/domain/configuration/
COPY host.xml appserver/domain/configuration/

RUN rm -fr /var/fluig

COPY start-container /usr/local/bin/start-container
RUN chmod +x /usr/local/bin/start-container

EXPOSE 8080 7777 8888 8000 8983 9990

ENTRYPOINT ["start-container"]
