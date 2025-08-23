# Gerenciamento de Inicialização e Serviços com systemd + systemctl

<details>
<summary class="summary">Sumário</summary>

- [O que é o systemd](#o-que-é-o-systemd)
- [O que é o systemctl](#o-que-é-o-systemctl)
- [Comandos mais usados no dia a dia DevOps](#comandos-mais-usados-no-dia-a-dia-devops)
- [Pontos importantes para DevOps](#pontos-importantes-para-devops)
- [Boas Práticas DevOps com systemd](#boas-práticas-devops-com-systemd)
- [Resumo rápido para consultas](#resumo-rápido-para-consultas)
</details>

> ### O que é o systemd

O **systemd** é o **gerenciador de inicialização (init system)** padrão nas distribuições Linux modernas (Ubuntu, CentOS, RHEL, Debian, etc.).

Ele substitui sistemas mais antigos como o **SysVinit** e o **Upstart**.

**Funções principais do systemd:**

- Gerenciar **serviços e processos de inicialização**.
- Definir **ordem de inicialização** com base em dependências.
- Fornecer **paralelismo/multiprocessamento** (serviços sobem de forma eficiente).
- Gerenciar **rede, sockets, dispositivos e timers**.
- Criar **logs integrados** via `journald`.

> ### O que é o systemctl

O **systemctl** é a ferramenta de linha de comando usada para interagir com o systemd.

Com ele, podemos verificar status, iniciar, parar, reiniciar e configurar a inicialização de serviços.

&nbsp;

> ### Comandos mais usados no dia a dia DevOps

**Ver status de um serviço:**

```bash
systemctl status <nome-do-serviço>
```

**Iniciar um serviço:**

```bash
systemctl start <nome-do-serviço>
```

**Parar um serviço:**

```bash
systemctl stop <nome-do-serviço>
```

**Reiniciar um serviço:**

```bash
systemctl restart <nome-do-serviço>
```

**Recarregar configurações (sem reiniciar):**

```bash
systemctl reload <nome-do-serviço>
```

**Habilitar para iniciar no boot:**

```bash
systemctl enable <nome-do-serviço>
```

**Desabilitar no boot:**

```bash
systemctl disable <nome-do-serviço>
```

**Listar todos os serviços ativos:**

```bash
systemctl list-units --type=service
```

**Mostrar dependências de um serviço:**

```bash
systemctl list-dependencies <nome-do-serviço>
```

&nbsp;

> ### Pontos importantes para DevOps

- **Automação de serviços**:
    
    Garantir que serviços como **Docker, Nginx, MySQL, Kubernetes** subam automaticamente no boot.
    
    Exemplo:
    
    ```bash
    systemctl enable docker
    ```
    
- **Troubleshooting**:
    
    Ao investigar falhas em pipelines ou aplicações, muitas vezes o problema está em um serviço que não inicializou corretamente.
    
    Use:
    
    ```bash
    systemctl status nginx
    journalctl -u nginx
    ```
    
- **Timers (cron moderno)**:
    
    O systemd substitui `cron` em muitas situações através de **timers**:
    
    ```bash
    systemctl list-timers
    ```
    
- **Isolamento de ambientes**:
    
    O systemd suporta **cgroups** (controle de recursos) para limitar memória, CPU e I/O de serviços — útil para evitar que um processo consuma todos os recursos da máquina.

&nbsp;

> ### Boas Práticas DevOps com systemd

| Prática | Benefício |
| --- | --- |
| Usar `systemctl enable` para serviços críticos | Evita indisponibilidade após reboot |
| Sempre verificar logs com `journalctl -u <serviço>` | Diagnóstico rápido de falhas |
| Usar `reload` ao invés de `restart` quando possível | Evita downtime desnecessário |
| Criar **unidades customizadas** (`.service`) para apps próprias | Padroniza deploys |
| Integrar timers do systemd no lugar de cron jobs simples | Mais confiável e integrado |

&nbsp;

> ### Resumo rápido para consultas

| Comando | Função |
| --- | --- |
| `systemctl status nginx` | Ver status do serviço |
| `systemctl start nginx` | Iniciar serviço |
| `systemctl stop nginx` | Parar serviço |
| `systemctl restart nginx` | Reiniciar serviço |
| `systemctl enable nginx` | Habilitar no boot |
| `systemctl disable nginx` | Desabilitar no boot |
| `journalctl -u nginx` | Logs do serviço |
| `systemctl list-units --type=service` | Listar serviços ativos |

&nbsp;

Esse conteúdo é **fundamental** para qualquer DevOps, pois praticamente todos os serviços críticos em servidores Linux (banco de dados, webservers, ferramentas de CI/CD, Docker, Kubernetes, etc.) são gerenciados via **systemd**.