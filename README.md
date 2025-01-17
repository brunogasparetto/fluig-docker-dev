# Fluig em Docker (para desenvolvimento)

Configuração para ter o Fluig rodando em um ambiente de desenvolvimento em Docker.

Esse repositório **NÃO CONTÉM** o instalador do Fluig, pois somente usuários credenciados podem baixar
o instalador.

A utilização do Fluig depende de um Servidor de Licenças válido. Caso não configure um Servidor
de Licenças no Fluig você só conseguirá utilizar o modo demonstração por 7 dias.

Por enquanto, para facilitar, a configuração de domain e host são sobreescritas, pois encontrei
muita dificuldade em configurar automaticamente o Fluig para ouvir em todas as redes ao invés
de somente a do container.

## Containers

- Fluig;
- MySQL 8.0
- MailDev (para testar envio de e-mail);

## Iniciando

Baixe o instalador do Fluig para Linux na [Central de Download da TOTVS](https://suporte.totvs.com/portal/p/10098/suporte-fluig-download#000035/FLUIG%201.8/Fluig/).

Descompacte o conteúdo do arquivo na pasta `image/installer`;

Caso necessário altere o TimeZone no arquivo `.env` (por padrão está America/Sao_Paulo).
Isso só terá validade ao criar a imagem.

No terminal execute o comando `docker compose up -d` para levantar os serviços.

Na primeira vez que executar este comando o Docker fará o build da imagem do Fluig,
rodando a instalação e efetuando as configurações.

Lembre-se: ao instalar o Fluig será necessário criar a empresa entrando no WCMAdmin.

- Login: wcmadmin
- Senha: adm

O diretório `/var/fluig-volume` é persistido no Docker, por isso o utilize como diretório
base para os volumes das empresas criadas. Assim cada empresa deve ter seu volume indicado
como um subdiretório de `/var/fluig-volume`. Ex: `/var/fluig-volume/empresa001`.

O Fluig será acessado no endereço <http://127.0.0.1:8080>

O MailDev (para visualizar os e-mails) será acessado no endereço <http://127.0.0.1:1080>

O banco de dados está acessível em `localhost` na porta `3306`.

Caso tente acessar o banco de dados pelo DBeaver é necessário alterar o tipo de conexão para URL e colocar a seguinte URL: `jdbc:mysql://localhost:3306/fluig?allowPublicKeyRetrieval=true&useSSL=false`

- Usuário: root
- Senha: rootpassword

## Comandos

Os comandos a seguir são executados no terminal.

- Iniciar os serviços: `docker compose up -d`;
- Parar os serviços: `docker compose stop`;
- Parar os serviços e deletar os containers¹: `docker compose down`;
- Entrar no bash do container do Fluig para executar comandos: `docker compose exec fluig bash`;
- Visualizar o log do Fluig (primeiro entre no bash do container): `log`;

¹ Se ao deletar os containers quiser uma instalação do zero é importante remover o volume criado,
na aplicação Docker, para não influenciar na nova instalação.
