# Fluig em Docker (para desenvolvimento)

Configuração para ter o Fluig rodando em um ambiente de desenvolvimento em Docker.

Por enquanto, para facilitar, a configuração de domain e host são sobreescritas, pois encontrei
muita dificuldade em configurar automaticamente o Fluig para ouvir em todas as redes ao invés
de somente a do container.

## Containers

- Fluig;
- MySQL 8.0
- MailHog (para testar envio de e-mail);

## Como levantar o serviço

Primeiro deve-se baixar o instalador do Fluig na [Central de Download da TOTVS](https://suporte.totvs.com/portal/p/10098/suporte-fluig-download#000035/FLUIG%201.8/Fluig/) (arquivo compactado);

Descompacte o conteúdo do arquivo na pasta `image/installer`;

Rode o comando `docker compose up -d`

Na primeira vez o docker fará o build da imagem e ao levantar o container
pela primeira vez fará a instalação do Fluig.

Caso ocorra algum erro pode parar o container e levantá-lo novamente para
que o script do Fluig execute novamente.

Ou pode entrar no Container com o comando `docker compose fluig exec bash`
para reiniciar o serviço do Fluig: `service fluig restart`

Pode usar o diretório `/var/fluig-volume` como volume para o Fluig.

## Como usar

Após o serviço levantado será possível acessar o Fluig pelo endereço http://localhost:8080

É possível visualizar os e-mails enviados no painel do MailHog, acessando o endereço http://localhost:8025

O banco de dados está acessível no localhost na porta 3306. Caso tente acessá-lo
pelo DBeaver é necessário alterar o tipo de conexão para URL e colocar a seguinte URL:
`jdbc:mysql://localhost:3306/fluig?allowPublicKeyRetrieval=true&useSSL=false`

Para executar comandos dentro do container do Fluig utilize o comando
`docker compose fluig exec bash`

## Encerrando

Para parar os containers basta executar o comando `docker compose stop`.

Caso queira deletar os containers pode executar `docker compose down`.
