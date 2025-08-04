
# Documentação: Instalação do Zabbix Server com Docker no Linux Mint 22.1

---

## 1. Pré-requisitos

- Linux Mint 22.1 (base Ubuntu 22.04)
- Docker instalado
- Docker Compose instalado

### Comandos para instalar Docker e Docker Compose:

```bash
sudo apt update
sudo apt install docker.io docker-compose -y
```

---

## 2. Preparação do ambiente

Criar diretório para o projeto Zabbix Docker:

```bash
mkdir ~/zabbix-docker
cd ~/zabbix-docker
```

---

## 3. Criação do arquivo docker-compose.yml

Criar o arquivo:

```bash
nano docker-compose.yml
```

Conteúdo do arquivo `docker-compose.yml`:

```yaml
version: '3.5'

services:
  postgres:
    image: postgres:13
    environment:
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: zabbixpass
      POSTGRES_DB: zabbix
    volumes:
      - pg_data:/var/lib/postgresql/data
    restart: always

  zabbix-server:
    image: zabbix/zabbix-server-pgsql:alpine-6.0-latest
    environment:
      DB_SERVER_HOST: postgres
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: zabbixpass
      POSTGRES_DB: zabbix
      ZBX_DEBUGLEVEL: 3
    depends_on:
      - postgres
    ports:
      - "10051:10051"
    restart: always

  zabbix-web:
    image: zabbix/zabbix-web-nginx-pgsql:alpine-6.0-latest
    environment:
      DB_SERVER_HOST: postgres
      POSTGRES_USER: zabbix
      POSTGRES_PASSWORD: zabbixpass
      POSTGRES_DB: zabbix
      ZBX_SERVER_HOST: zabbix-server
      PHP_TZ: America/Sao_Paulo
    depends_on:
      - zabbix-server
    ports:
      - "8090:8080"
    restart: always

volumes:
  pg_data:
```

---

## 4. Subir o ambiente Docker

No diretório do projeto, execute:

```bash
docker-compose up -d
```

Este comando baixa as imagens e inicializa os containers.

---

## 5. Verificar containers ativos

```bash
docker ps
```

Deve mostrar os containers:

- postgres
- zabbix-server
- zabbix-web

---

## 6. Acessar o frontend do Zabbix

Abra o navegador e acesse:

```
http://localhost:8090
```

ou, se for acessar de outra máquina, substitua `localhost` pelo IP da máquina.

---

## 7. Credenciais padrão

- Usuário: `Admin`
- Senha: `zabbix`

**Observação:** O usuário `Admin` tem “A” maiúsculo.

---

## 8. Configurações recomendadas pós-login

- Alterar a senha do usuário Admin.
- Criar usuários com permissões específicas para diferentes perfis.
- Adicionar hosts para monitoramento, como máquinas Linux com o Zabbix Agent instalado.

---

## 9. Próximos passos (opcionais)

- Instalar o Zabbix Agent nas máquinas que deseja monitorar.
- Configurar templates e triggers no painel Zabbix.
- Criar dashboards personalizados.
