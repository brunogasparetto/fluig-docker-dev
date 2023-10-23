# Fluig em Docker (para desenvolvimento)

Configuração para ter o Fluig rodando em um ambiente de desenvolvimento em Docker.

Por enquanto, para facilitar, a configuração de domain e host são sobreescritas, pois encontrei
muita dificuldade em configurar automaticamente o Fluig para ouvir em todas as redes ao invés
de somente a do container.

## Containers

- Fluig;
- MySQL 8.0
- MailHog (para testar envio de e-mail);

## Iniciando

Baixe o instalador do Fluig para Linux na [Central de Download da TOTVS](https://suporte.totvs.com/portal/p/10098/suporte-fluig-download#000035/FLUIG%201.8/Fluig/).

Descompacte o conteúdo do arquivo na pasta `image/installer`;

No terminal execute o comando `docker compose up -d` para levantar os serviços.

Na primeira vez que executar este comando o Docker fará o build da imagem do Fluig,
rodando a instalação e efetuando as configurações.

Lembre-se: ao instalar o Fluig será necessário criar a empresa entrando no WCMAdmin.

Login: wcmadmin<br>
Senha: adm

O diretório `/var/fluig-volume` é persistido no Docker, por isso o utilize como diretório
base para os volumes das empresas criadas. Assim cada empresa deve ter seu volume indicado
como um subdiretório de `/var/fluig-volume`. Ex: `/var/fluig-volume/empresa001`.

O Fluig será acessado no endereço http://127.0.0.1:8080

O MailHog (para visualizar os e-mails) será acessado no endereço http://127.0.0.1:8025

O banco de dados está acessível em `localhost` na porta `3306`.

Caso tente acessar o banco de dados pelo DBeaver é necessário alterar o tipo de conexão para URL e colocar a seguinte URL: `jdbc:mysql://localhost:3306/fluig?allowPublicKeyRetrieval=true&useSSL=false`

Usuário: root<br>
Senha: rootpassword

## Comandos

Os comandos a seguir são executados no terminal.

- Iniciar os serviços: `docker compose up -d`;
- Parar os serviços: `docker compose stop`;
- Parar os serviços e deletar os containers¹: `docker compose down`;
- Entrar no container do Fluig para executar comandos: `docker compose fluig exec bash`;
- Visualizar o log do Fluig: `log` (é um alias para `tail -f appserver/domain/servers/fluig1/log/server.log`);

¹ Se ao deletar os containers quiser uma instalação do zero é importante remover o volume criado,
na aplicação Docker, para não influenciar na nova instalação.
