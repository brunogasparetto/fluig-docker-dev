# Guia de Instalação — Fluig 2.0.0 ou 1.8.2 no Docker

## Pré-requisitos

- **Docker Desktop** instalado e em execução (Windows, Mac ou Linux)
- **Git** para clonar o repositório
- **Mínimo 8 GB de RAM** disponível para o Docker (recomendado 12 GB)
- Acesso ao portal TOTVS para baixar o instalador do Fluig

---

## 1. Clonar o repositório

```bash
git clone https://github.com/seu-usuario/fluig-docker-dev.git
cd fluig-docker-dev
```

---

## 2. Baixar e extrair o instalador do Fluig

Acesse o portal de suporte TOTVS e baixe o instalador para Linux do **Fluig 2.0.0** ou
do **Fluig 1.8.2**.

Extraia o conteúdo dentro da pasta `image/installer/`. A estrutura deve ficar assim:

```
image/installer/
├── fluig-installer-64.sh
├── fluig-installer.jar
├── wildfly-dist.zip
├── openoffice.zip
├── jdk-64/
└── packs/
```

> Esta pasta está no `.gitignore` e nunca será enviada ao repositório.

---

## 3. Configurar o arquivo `.env` (opcional)

O arquivo `.env` já vem configurado com valores padrão. Edite se necessário:

```env
FLUIG=2.0              # Indica a versão do Fluig. Valores possíveis são: 2.0 ou 1.8
TZ=America/Sao_Paulo   # Timezone do servidor
INSTALL_NODE=false     # true = instala o módulo RealTime (Node.js)
INSTALL_SOLR=false     # true = instala o indexador Solr
```

> Manter `INSTALL_NODE=false` e `INSTALL_SOLR=false` reduz o uso de memória e o tempo de build.
> Qualquer alteração aqui exige rebuild completo da imagem.

---

## 4. Fazer o build e subir os serviços

```bash
docker compose up -d
```

Na **primeira execução** o Docker vai buildar a imagem — isso inclui rodar o instalador do Fluig e
leva **10-15 minutos**. Acompanhe o progresso:

```bash
docker compose logs -f
```

Quando o build terminar, o container do Fluig será iniciado automaticamente.

---

## 5. Aguardar o Fluig inicializar

Após o build, o servidor de aplicação (WildFly) leva mais **4-6 minutos** para subir completamente.
Monitore o log:

```bash
docker compose logs -f fluig
```

Aguarde a mensagem abaixo antes de prosseguir:

```
Fluig is up and running right now.
```

---

## 6. Criar a empresa no WCMAdmin

O Fluig exige a criação de uma empresa antes de poder ser utilizado.

1. Acesse **http://127.0.0.1:8080/wcmadmin**
2. Login: `wcmadmin` / Senha: `adm`
3. Vá em **Empresas** > **Nova Empresa**
4. Preencha os campos:

   | Campo | Valor sugerido |
   |-------|----------------|
   | Identificador | `empresa001` |
   | Código | `1` |
   | Descrição | `Empresa de Desenvolvimento` |
   | CNPJ | `00.000.000/0001-00` |
   | Diretório default | `/var/fluig-volume/empresa001` |

5. Deixe **"Deseja habilitar o encerramento de sessão no Identity?"** como `Não`
6. Clique em **Salvar** e aguarde a empresa ser provisionada

---

## 7. Configurar servidor de licenças (opcional)

Se você possui um Servidor de Licenças TOTVS, configure-o no WCMAdmin, em parâmetros da plataforma.

> Sem licença, o Fluig funciona em **modo demonstração por 7 dias**.

---

## 8. Acessar o Fluig

Com a empresa criada, acesse o Fluig pelo navegador:

**http://127.0.0.1:8080**

As credenciais serão as do usuário administrador que criou para a empresa.

---

## Serviços disponíveis

| Serviço | URL | Credenciais |
|---------|-----|-------------|
| Fluig | http://127.0.0.1:8080 | wcmadmin / adm |
| WCMAdmin | http://127.0.0.1:8080/wcmadmin | wcmadmin / adm |
| MailDev (e-mails de teste) | http://127.0.0.1:1080 | — |
| MySQL | localhost:3306 | root / rootpassword |
| JBoss Management | http://127.0.0.1:9990 | — |

Conexão MySQL para DBeaver (usar tipo URL):
```
jdbc:mysql://localhost:3306/fluig?allowPublicKeyRetrieval=true&useSSL=false
```

---

## Comandos do dia a dia

```bash
# Iniciar os serviços
docker compose up -d

# Parar os serviços (mantém dados)
docker compose stop

# Remover containers (volumes e dados persistem)
docker compose down

# Ver logs em tempo real
docker compose logs -f fluig

# Abrir terminal dentro do container
docker compose exec fluig bash

# Ver log do WildFly (dentro do container do 2.0.0)
tail -f /opt/fluig/appserver/standalone/log/server.log

# Ver log do WildFly (dentro do container do 1.8.2)
tail -f /opt/fluig/appserver/domain/servers/fluig1/log/server.log

# Ver log do WildFly (alias que funciona em ambas versões)
log
```

---

## Reconstruir a imagem

Necessário ao alterar `.env`, `install.conf`, `Dockerfile` ou `start-container`:

```bash
docker compose build --no-cache
docker compose up -d
```

---

## Resetar tudo (instalação limpa)

```bash
# Para e remove os containers
docker compose down

# Lista os volumes
docker volume ls

# Remove o volume do Fluig (APAGA TODOS OS DADOS)
docker volume rm fluig-docker-dev_fluig-volume

# Rebuild e reinicia
docker compose up -d --build
```

---

## Solução de problemas

**Página em branco ou conexão recusada em :8080**
- O WildFly ainda está subindo. Aguarde 4-6 min e recarregue.
- Verifique os logs: `docker compose logs -f fluig`
- Confirme que o WildFly está ouvindo na porta correta (dentro do container):
  ```bash
  docker compose exec fluig bash -c "cat /proc/net/tcp | grep 1F90"
  ```
  Deve retornar uma linha começando com `00000000:1F90` (0.0.0.0:8080).

**Erro `server_name deve ser informado` durante o build**
- O `install.conf` está no formato antigo (1.8.x). Use o arquivo do repositório que já está no formato 2.0.0.

**Erro `bash\r: No such file or directory` durante o build**
- O script `start-container` tem quebras de linha Windows (CRLF). O Dockerfile já corrige isso automaticamente com
`sed -i 's/\r//'`. Se persistir, converta o arquivo para LF manualmente.

**`License Service DOWN`** nos logs
- Normal quando não há Servidor de Licenças. O Fluig funciona por 7 dias em modo demo.

**MySQL não saudável / Fluig não inicia**
- Aguarde o MySQL terminar de inicializar (~30 segundos). O `docker compose up` já aguarda automaticamente pelo healthcheck.
- Se persistir: `docker compose down && docker compose up -d`
