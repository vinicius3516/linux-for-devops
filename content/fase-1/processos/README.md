# Gerenciamento de Processos no Linux

<details>
<summary class="summary">Sumário</summary>


</details>

> ### O que é um Processo

No Linux, um **processo** é uma instância de um programa em execução.

Quando um software é iniciado, o sistema cria um processo com um **PID (Process ID)** único, que identifica essa execução até que ela termine.

Conceitos importantes:

- **PID** → Número único do processo.
- **PPID** → PID do processo pai (que originou o atual).
- **UID** → Usuário que está executando o processo.
- **GID** → Grupo associado ao processo.

> ### Sinais de Processos (Signals)

Sinais permitem interagir ou controlar processos (pausar, finalizar, reiniciar).

Principais sinais usados no dia a dia:

| Sinal | Número | Ação |
| --- | --- | --- |
| `SIGHUP` | 1 | Enviado quando o terminal de controle é fechado. |
| `SIGINT` | 2 | Interrompe o processo (CTRL+C). |
| `SIGKILL` | 9 | Força a finalização (não pode ser ignorado). |
| `SIGTERM` | 15 | Solicita finalização de forma limpa (padrão do `kill`). |

Lista completa de sinais:

```bash
man 7 signal
```

> ### Comandos para Listar Processos

Processos do terminal atual:

```bash
ps
ps -xf    # Mais detalhes
```

Todos os processos do sistema:

```bash
ps -ef
```

Processos de um usuário específico (root pode ver todos):

```bash
ps -fu vagrant
```

Exibir como árvore hierárquica:

```bash
ps -xf --forest
```

Filtrar colunas:

```bash
ps -xo pid,command
```

> ### Filtrando Processos

Com `grep`:

```bash
ps -ef | grep sshd
```

Com `pgrep` (mais direto):

```bash
pgrep sshd             # Lista PIDs
pgrep -c sshd           # Conta processos
pgrep -G 1000           # Filtra por GID
pgrep -U vagrant        # Filtra por usuário
pgrep -l sshd           # Lista nomes
pgrep -a sshd           # Lista completo (PID + comando)
pgrep -oa               # Processo mais antigo
pgrep -na               # Processo mais recente
```

> ### Monitoramento em Tempo Real

`top` – Monitor interativo:

```bash
top
```

- `M` → Ordena por uso de memória
- `P` → Ordena por uso de CPU
- `V` → Mostra árvore de processos
- `k` → Mata processo informando PID

Alternativa moderna: `htop` (mais visual).

> ### Finalizando Processos

Pelo nome:

```bash
pkill -15 nome_processo
pkill -TERM nome_processo
```

Pelo PID:

```bash
kill -15 PID
kill -9 PID   # Força finalização
```

Combinando `pgrep` + `kill`:

```bash
kill -15 $(pgrep httpd)
```

---

> ### Boas Práticas DevOps

| Prática | Benefício |
| --- | --- |
| Usar `SIGTERM` antes do `SIGKILL` | Permite encerramento limpo |
| Monitorar serviços críticos com `top`/`htop` | Detecta consumo excessivo de recursos |
| Automatizar verificações com `pgrep` | Scripts mais simples e robustos |
| Evitar matar processos essenciais do sistema | Previne queda do servidor |
| Registrar PIDs em arquivos | Facilita automações (`/var/run/*.pid`) |

> ### Dicas rápidas

| Comando | Função |
| --- | --- |
| `ps -ef` | Lista todos os processos |
| `pgrep nome` | Encontra PID de processo |
| `top` | Monitora processos em tempo real |
| `kill PID` | Finaliza processo pelo PID |
| `pkill nome` | Finaliza processo pelo nome |
| `man 7 signal` | Lista sinais disponíveis |

> ### Resumo rápido

- **PID** = identificador do processo.
- **Signals** = controlam execução (`SIGHUP`, `SIGINT`, `SIGTERM`, `SIGKILL`).
- **Listagem** = `ps`, `pgrep`, `top`.
- **Finalização** = `kill`, `pkill`.
- **Monitoramento** = `top`, `htop`.