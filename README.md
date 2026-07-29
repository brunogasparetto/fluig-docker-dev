# Fluig em Docker (para desenvolvimento)

Ambiente de desenvolvimento local do **Fluig 2.0.0** ou **Fluig 1.8.2** (TOTVS) rodando em Docker.

> O repositório **não contém** o instalador do Fluig. Ele deve ser baixado separadamente no portal TOTVS e extraído em `image/installer/` antes do build.

Para o passo a passo completo de instalação, consulte [INSTALACAO.md](INSTALACAO.md).

---

## Pré-requisitos

- Docker Desktop instalado e em execução
- Mínimo **8 GB de RAM** disponível para o Docker
- Instalador para Linux do Fluig 2.0.0 ou 1.8.2, extraído em `image/installer/`

---

## Início rápido

```bash
# 1. Extraia o instalador do Fluig em image/installer/

# 2. Suba os serviços (build na primeira vez — ~15 min)
docker compose up -d

# 3. Acompanhe até aparecer "Fluig is up and running right now."
docker compose logs -f fluig

# 4. Acesse http://127.0.0.1:8080/wcmadmin e crie uma empresa
#    Login: wcmadmin / Senha: adm

# 5. Acesse http://127.0.0.1:8080
```

---

## Serviços

| Serviço | URL | Credenciais |
|---------|-----|-------------|
| Fluig | http://127.0.0.1:8080 | wcmadmin / adm |
| WCMAdmin | http://127.0.0.1:8080/wcmadmin | wcmadmin / adm |
| MailDev | http://127.0.0.1:1080 | — |
| MySQL | localhost:3306 | root / rootpassword |

MySQL URL para DBeaver:
```
jdbc:mysql://localhost:3306/fluig?allowPublicKeyRetrieval=true&useSSL=false
```

---

## Configuração (.env)

| Variável | Padrão | Descrição |
|----------|--------|-----------|
| `FLUIG` | `2.0` | Versão do Fluig que será instalada. Pode ser 2.0 ou 1.8 |
| `TZ` | `America/Sao_Paulo` | Timezone do servidor |
| `INSTALL_NODE` | `false` | Instala o módulo RealTime (Node.js) |
| `INSTALL_SOLR` | `false` | Instala o indexador Solr |

Alterações no `.env` exigem rebuild da imagem.

---

## Comandos úteis

```bash
# Parar os serviços (mantém dados)
docker compose stop

# Remover containers (volumes persistem)
docker compose down

# Shell dentro do container
docker compose exec fluig bash

# Log do WildFly (dentro do container)
tail -f /opt/fluig/appserver/standalone/log/server.log

# Há um alias para o Log do WildFly (dentro do container)
log
```

---

## Dados persistentes

Os dados ficam no volume Docker mapeado em `/var/fluig-volume`. Ao criar a empresa no WCMAdmin, use um subdiretório como `/var/fluig-volume/empresa001`.

Para iniciar do zero após `docker compose down`:

```bash
docker volume rm fluig-docker-dev_fluig-volume
```

---

## Licença

Sem um Servidor de Licenças TOTVS em `localhost:5555`, o Fluig roda em **modo demonstração por 7 dias**. Configure o endereço do LS no WCMAdmin, em Parâmetros da Plataforma.
