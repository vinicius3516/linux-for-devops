# Logs e troubleshooting com journalctl e /var/log

<details>
<summary class="summary">Sumário - Gerenciamento de Logs no Linux – /var/log</summary>


- [O que é o `/var/log`](#o-que-é-o-varlog)
- [Funções do `/var/log`](#funções-do-varlog)
- [Principais arquivos em `/var/log`](#principais-arquivos-em-varlog)
- [Como usar logs no dia a dia DevOps](#como-usar-logs-no-dia-a-dia-devops)
- [Ferramentas úteis para análise de logs](#ferramentas-úteis-para-análise-de-logs)
- [Boas práticas DevOps com logs](#boas-práticas-devops-com-logs)
- [Dicas rápidas](#dicas-rápidas)
- [Resumo rápido para consultas](#resumo-rápido-para-consultas)
</details>

<details>
<summary class="summary">Sumário - Gerenciamento de Logs com journalctl</summary>


- [O que é o journalctl](#o-que-é-o-journalctl)
- [Funções principais](#funções-principais)
- [Principais parâmetros do journalctl](#principais-parâmetros-do-journalctl)
- [Exemplos práticos no dia a dia DevOps](#exemplos-práticos-no-dia-a-dia-devops)
- [Boas práticas para DevOps](#boas-práticas-para-devops)
- [Resumo rápido](#resumo-rápido)
</details>

> ### O que é o `/var/log`

O diretório **`/var/log`** é o **repositório central de logs do Linux**, onde são armazenados registros de eventos do sistema, do kernel, de autenticação, de serviços e de aplicações.

Para um **DevOps**, esse diretório é essencial em **troubleshooting, auditoria, monitoramento e segurança**.

---

> ### Funções do `/var/log`

- **Centralizar informações**
    
    Reúne eventos de todo o sistema e serviços (rede, autenticação, web servers, banco de dados etc.).
    
- **Facilitar troubleshooting**
    
    Ajuda a identificar falhas em pipelines, serviços que não subiram ou erros em aplicações.
    
- **Monitorar segurança**
    
    Registra logins, falhas de autenticação e ações do root, permitindo identificar acessos suspeitos.
    
- **Acompanhar desempenho**
    
    Guarda histórico de tarefas agendadas, jobs do cron, consumo de serviços e erros de performance.
    

> ### Principais arquivos em `/var/log`

| Arquivo | Função |
| --- | --- |
| `/var/log/syslog` ou `/var/log/messages` | Mensagens gerais do sistema, inicialização, eventos diversos |
| `/var/log/auth.log` ou `/var/log/secure` | Logins, sudo, falhas de autenticação |
| `/var/log/kern.log` | Eventos do kernel (drivers, hardware, falhas) |
| `/var/log/cron` | Execução de jobs agendados no `cron` |
| `/var/log/dmesg` | Logs de inicialização do kernel (hardware, módulos) |
| `/var/log/apache2/*.log` ou `/var/log/httpd/*.log` | Logs do Apache (erros e acessos) |
| `/var/log/nginx/*.log` | Logs do Nginx (erros e acessos) |
| `/var/log/mysql/*.log` ou `/var/log/mariadb/*.log` | Logs de banco de dados |
| `/var/log/maillog` | Logs de servidores de e-mail |

Cada aplicação pode criar seu próprio log dentro de `/var/log`.



> ### Como usar logs no dia a dia DevOps

**Ver conteúdo completo:**

```bash
cat /var/log/syslog
```

**Seguir log em tempo real:**

```bash
tail -f /var/log/syslog
```

**Visualizar últimas linhas:**

```bash
tail -n 50 /var/log/auth.log
```

**Buscar palavra-chave em logs:**

```bash
grep "ERROR" /var/log/nginx/error.log
```

**Usar `less` para navegação:**

```bash
less /var/log/kern.log
```

---

> ### Ferramentas úteis para análise de logs

- `journalctl -u <serviço>` → logs integrados do systemd
- `tail -f arquivo.log` → acompanhar em tempo real
- `grep | awk | sed` → filtrar e manipular logs
- `logrotate` → gerenciar rotação/arquivamento de logs (evita que cresçam indefinidamente)
- **Stacks de observabilidade** → Centralizar e visualizar logs:
    - ELK Stack (Elasticsearch + Logstash + Kibana)
    - Loki + Promtail + Grafana
    - Splunk

---

> ### Boas práticas DevOps com logs

| Prática | Benefício |
| --- | --- |
| Centralizar logs (Graylog, ELK, Loki) | Melhor análise em ambientes distribuídos |
| Monitorar `/var/log/auth.log` | Detectar acessos indevidos |
| Configurar `logrotate` | Evitar problemas de espaço em disco |
| Automatizar parsing com `grep`/`awk` | Ganha tempo em troubleshooting |
| Usar `journalctl` em sistemas com systemd | Acesso unificado e filtrado por serviço |

---

> ### Dicas rápidas

| Comando | Função |
| --- | --- |
| `tail -f /var/log/syslog` | Acompanhar log do sistema em tempo real |
| `grep "sshd" /var/log/auth.log` | Ver tentativas de login SSH |
| `journalctl -xe` | Mostrar logs recentes com erros |
| `du -sh /var/log/*` | Checar espaço ocupado por logs |
| `logrotate -f /etc/logrotate.conf` | Forçar rotação de logs |

---

> ### Resumo rápido para consultas

- **Logs do sistema** → `/var/log/syslog` ou `/var/log/messages`
- **Segurança/autenticação** → `/var/log/auth.log`
- **Kernel** → `/var/log/kern.log`, `dmesg`
- **Agendamentos** → `/var/log/cron`
- **Servidores web** → `/var/log/apache2/` ou `/var/log/nginx/`
- **Banco de dados** → `/var/log/mysql/`
- **Comandos-chave** → `tail -f`, `grep`, `journalctl`

---

Esse é um dos diretórios **mais críticos no dia a dia DevOps**, porque quando algo dá errado (um container não sobe, um deploy falha, um serviço não responde), é **no `/var/log` que você vai encontrar as pistas**.

&nbsp;

# Gerenciamento de Logs com `journalctl`

> ### O que é o journalctl

O **journalctl** é o utilitário de linha de comando do **systemd-journald**, responsável por coletar e centralizar logs do sistema, kernel e serviços em um formato **binário estruturado**.

**Principais vantagens para DevOps:**

- **Centralização** → unifica logs do sistema e serviços.
- **Filtragem avançada** → por data, unidade, severidade, usuário, etc.
- **Formatos diversos** → saída em texto simples ou JSON.
- **Monitoramento em tempo real** → substitui `tail -f`.

---

> ### Funções principais

- **Monitoramento em tempo real** (`f`)
- **Solução de problemas** (falhas de serviços, kernel, hardware)
- **Auditoria de segurança** (logins, sudo, erros de autenticação)
- **Análise temporal** (`-since`, `-until`)
- **Centralização de logs** (sem depender apenas de `/var/log`)

---

> ### Principais parâmetros do `journalctl`

**Logs do sistema e inicialização**

```bash
journalctl -b          # Logs da inicialização atual
journalctl -b -1       # Logs da inicialização anterior
journalctl --list-boots  # Lista inicializações disponíveis
journalctl -k          # Mostra apenas logs do kernel
```

**Navegação e ordenação**

```bash
journalctl             # Mostra todos os logs (mais antigos → recentes)
journalctl -r          # Ordem invertida (mais recentes primeiro)
journalctl -f          # Segue em tempo real (como tail -f)
journalctl -n 50       # Últimas 50 linhas de log
journalctl -e          # Vai direto para o final dos logs
```

**Filtrar por tempo**

```bash
journalctl --since "2025-08-23"              # Logs a partir de uma data
journalctl --since "1 hour ago"              # Última hora
journalctl --since "2025-08-10" --until "2025-08-23"  # Entre duas datas
journalctl --since "10 min ago"              # Últimos 10 minutos
journalctl --utc                             # Converte para UTC
```

**Filtrar por unidade (serviço systemd)**

```bash
journalctl -u ssh           # Logs do serviço ssh
journalctl -u nginx -b      # Logs do nginx da inicialização atual
journalctl -u docker -r     # Logs do docker em ordem invertida
```

**Filtrar por prioridade**

- Severidades:
    - `0` = emerg
    - `1` = alert
    - `2` = crit
    - `3` = err
    - `4` = warning
    - `5` = notice
    - `6` = info
    - `7` = debug

```bash
journalctl -p err           # Apenas erros
journalctl -p crit..alert   # Crítico até alertas
journalctl -u nginx -p warning --since "today"  # Avisos do nginx de hoje
```

**Filtrar por usuário ou PID**

```bash
journalctl _UID=1000        # Logs de um usuário específico
journalctl _PID=1234        # Logs de um processo pelo PID
journalctl _COMM=sshd       # Logs de um comando (ex: sshd)
```

- Descobrir UID de um usuário:

```bash
id <username>
```

**Formatos de saída**

```bash
journalctl -o short         # Padrão
journalctl -o verbose       # Completo, detalhado
journalctl -o json          # JSON simples
journalctl -o json-pretty   # JSON formatado
journalctl -o cat           # Apenas a mensagem do log
```

Exemplo útil:

```bash
journalctl -u docker --since "2025-08-23" -p err -o json-pretty
```

---

> ### Exemplos práticos no dia a dia DevOps

- Ver falhas de autenticação SSH:

```bash
journalctl -u ssh -p err
```

- Monitorar Docker em tempo real:

```bash
journalctl -fu docker
```

- Ver todos os logs de hoje:

```bash
journalctl --since today
```

- Exportar para análise:

```bash
journalctl -u nginx -p warning --since "yesterday" -o json > nginx-logs.json
```

---

> ### Boas práticas para DevOps

| Prática | Benefício |
| --- | --- |
| Sempre filtrar por unidade (-u) | Evita ruído em servidores com muitos serviços |
| Usar `--since` + `--until` | Reduz volume de logs em troubleshooting |
| Exportar em JSON (`-o json-pretty`) | Integração com ferramentas externas (SIEM, dashboards) |
| Monitorar em tempo real (`-f`) | Detecta incidentes na hora |
| Usar severidade (`-p`) | Priorização rápida em incidentes críticos |
| Automatizar parsing de logs | Scripts de monitoramento mais eficientes |

---

> ### Dicas rápidas

| Comando | O que faz |
| --- | --- |
| `journalctl -xe` | Mostra últimos logs com foco em erros |
| `journalctl -u docker -f` | Acompanha logs do Docker |
| `journalctl --disk-usage` | Mostra quanto espaço os logs ocupam |
| `journalctl --vacuum-size=500M` | Limita logs a 500 MB |
| `journalctl --vacuum-time=7d` | Mantém apenas 7 dias de logs |

---

> ### Resumo rápido

- **Centraliza logs do Linux (systemd)**.
- **Filtros principais:** tempo (`-since/--until`), unidade (`u`), severidade (`p`), usuário (`_UID`).
- **Saída:** texto ou JSON (`o`).
- **Útil para troubleshooting em produção**: monitoramento em tempo real, falhas de serviços, incidentes de segurança.

---

Esse é o complemento perfeito da aula de `/var/log`, já que **journalctl** é a forma moderna de acessar e filtrar esses registros em sistemas com systemd.