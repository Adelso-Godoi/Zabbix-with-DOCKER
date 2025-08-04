# Guia de Instalação e Configuração do Zabbix Agent no Ubuntu 24.04

Este guia apresenta o passo a passo usado para instalar e configurar o Zabbix Agent no Ubuntu 24.04
---

## 1. Adicionar o repositório oficial do Zabbix

Primeiro, baixe e instale o pacote de repositório do Zabbix:

```bash
wget https://repo.zabbix.com/zabbix/7.4/ubuntu/pool/main/z/zabbix-release/zabbix-release_7.4-1+ubuntu24.04_all.deb
sudo dpkg -i zabbix-release_7.4-1+ubuntu24.04_all.deb
sudo apt update
```

---

## 2. Instalar o Zabbix Agent

```bash
sudo apt install zabbix-agent
```

---

## 3. Configurar o Zabbix Agent

Edite o arquivo de configuração:

```bash
sudo nano /etc/zabbix/zabbix_agentd.conf
```

Altere os seguintes parâmetros:

```conf
Server=192.168.112.31          # IP da máquina onde está o servidor Zabbix (mesmo que esteja em container)
ServerActive=192.168.112.31
Hostname=CRM-NT-09341           # Nome do host que aparecerá no Zabbix Server
```

Salve e saia do arquivo (`Ctrl + O`, `Enter`, `Ctrl + X`).

---

## 4. Iniciar e habilitar o agent

```bash
sudo systemctl restart zabbix-agent
sudo systemctl enable zabbix-agent
```

Verifique se está funcionando:

```bash
sudo systemctl status zabbix-agent
```

---

## 5. Testar comunicação com o servidor Zabbix

Do servidor Zabbix (container), execute:

```bash
docker exec -it <id_container> bash
zabbix_get -s 192.168.112.30 -k system.uptime
```

Se receber um número (segundos de uptime), está funcionando corretamente.

---

## 6. Verificar métricas locais (opcional)

No próprio cliente:

```bash
zabbix_agentd -t system.uptime
zabbix_agentd -t system.hostname
zabbix_agentd -t vfs.fs.size[/,free]
```

---

## 7. Observações

- Certifique-se de que a porta 10050 (Zabbix Agent) esteja liberada para o servidor.
- O template padrão a ser utilizado é: `Template OS Linux by Zabbix agent`.
- Caso ocorra o erro de discovery duplicado, remova o template antigo antes de aplicar um novo.

