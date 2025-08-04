
# Passo a passo: Subir o Zabbix Agent com Docker

## Pré-requisitos
- Docker instalado no sistema
- Zabbix Server e Web já rodando (pelo Docker Compose ou não)
- Rede Docker do Zabbix Server conhecida (ex: `zabbix-docker_default`)

## 1. Criar diretório e arquivo de configuração

```bash
mkdir ~/zabbix-agent
cd ~/zabbix-agent
```

Crie o arquivo `zabbix_agentd.conf`:

```bash
nano zabbix_agentd.conf
```

Conteúdo sugerido:

```ini
PidFile=/tmp/zabbix_agentd.pid
LogFile=/tmp/zabbix_agentd.log
Server=172.22.0.0/16,192.168.112.31
ServerActive=172.22.0.0/16,192.168.112.31
Hostname=ZabbixDockerAgent
```

## 2. Subir o container do Zabbix Agent

```bash
sudo docker run -d \
  --name zabbix-agent \
  -v $(pwd)/zabbix_agentd.conf:/etc/zabbix/zabbix_agentd.conf:ro \
  --network zabbix-docker_default \
  zabbix/zabbix-agent:alpine-6.0-latest
```

## 3. Verificar se está rodando

```bash
sudo docker logs -f zabbix-agent
```

Saída esperada (exemplo):

```
Starting Zabbix Agent [ZabbixDockerAgent]...
no active checks on server [IP]: host [ZabbixDockerAgent] not found
```

## 4. Cadastrar no Zabbix Web

- Vá em **Configuration → Hosts**
- Clique em **Create host**
- Nome: `ZabbixDockerAgent`
- IP: IP do container (ex: `172.22.0.5`)
- Grupo: ex: `Linux Servers`
- Template: adicione o `Template OS Linux by Zabbix agent`

## 5. Testar conectividade

No container do Zabbix Server:

```bash
zabbix_get -s 172.22.0.5 -k agent.ping
```

Retorno esperado: `1`
--------------------------------
