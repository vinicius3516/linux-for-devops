# Estrutura de diretórios do Linux (FHS)

<details>
<summary class="summary">Fase-1</summary>

- [1. O que é o FHS](#o-que-é-o-fhs)
- [2. Principais diretórios e suas funções](#principais-diretórios-e-suas-funções)
- [3. Por que o DevOps precisa conhecer o FHS](#por-que-o-devops-precisa-conhecer-o-fhs)
- [4. Exemplos práticos de uso](#exemplos-práticos-de-uso)
- [5. Boas práticas para DevOps](#boas-práticas-para-devops)
- [6. Dicas rápidas de administração](#dicas-rapidas-de-administracao)
- [Resumo rápido para consultas](#resumo-rápido-para-consultas)
</details>

### O que é o FHS

O **Filesystem Hierarchy Standard (FHS)** define a organização e a estrutura de diretórios em sistemas Linux/Unix.

Ele estabelece **onde arquivos e diretórios devem estar localizados** para que sistemas e softwares funcionem de forma previsível.

Conhecer o FHS é fundamental para administrar sistemas, resolver problemas e automatizar tarefas no dia a dia DevOps.

&nbsp;

### Principais diretórios e suas funções

| Diretório | Função | Observações |
| --- | --- | --- |
| **`/bin`** | Binários essenciais para todos os usuários (root e comuns) | Contém comandos básicos como `ls`, `cp`, `mv`, `cat` |
| **`/boot`** | Arquivos necessários para inicializar o sistema | Kernel (`vmlinuz`), initramfs, bootloader (GRUB) |
| **`/dev`** | Arquivos de dispositivos (device files) | Inclui discos, pendrives, partições e dispositivos virtuais |
| **`/etc`** | Arquivos de configuração do sistema e aplicativos | Configurações globais, ex: `/etc/ssh/sshd_config` |
| **`/home`** | Diretórios pessoais dos usuários comuns | Ex: `/home/alice`, `/home/joao` |
| **`/lib`** | Bibliotecas essenciais para programas de `/bin` e `/sbin` | Equivalente a DLLs no Windows |
| **`/media`** | Pontos de montagem para dispositivos removíveis | Automount de pendrives, CDs, DVDs |
| **`/mnt`** | Pontos de montagem temporários | Usado manualmente por administradores |
| **`/opt`** | Aplicativos opcionais de terceiros | Ex: softwares comerciais |
| **`/root`** | Diretório pessoal do usuário root | Não confundir com `/` (raiz) |
| **`/sbin`** | Binários administrativos (somente root) | Ex: `fsck`, `ifconfig`, `shutdown` |
| **`/srv`** | Dados de serviços (web, FTP, etc.) | Ex: `/srv/ftp`, `/srv/www` |
| **`/tmp`** | Arquivos temporários (voláteis) | Limpados no reboot |
| **`/usr`** | Aplicativos e arquivos para uso de usuários | Subpastas como `/usr/bin`, `/usr/lib`, `/usr/share` |
| **`/var`** | Arquivos variáveis do sistema | Logs, cache, spool, bancos de dados temporários |

&nbsp;

### Por que o DevOps precisa conhecer o FHS

| Motivo | Impacto na prática |
| --- | --- |
| **Troubleshooting rápido** | Encontrar logs e configs sem perder tempo |
| **Automação** | Scripts que sabem onde procurar arquivos |
| **Segurança** | Saber onde ficam arquivos sensíveis para backup e proteção |
| **Monitoramento** | Identificar pontos críticos como `/var/log` ou `/tmp` |
| **Gerenciamento de espaço** | Saber onde limpar cache ou arquivos temporários |

&nbsp;

### Exemplos práticos de uso

Ver logs do sistema:

```bash
cat /var/log/syslog
journalctl -xe
```

Ver pacotes baixados mas não instalados (Debian/Ubuntu):

```bash
ls /var/cache/apt/archives
```

Montar um disco manualmente em `/mnt`:

```bash
sudo mount /dev/sdb1 /mnt
```

Editar configuração de um serviço:

```bash
sudo nano /etc/ssh/sshd_config
```

&nbsp;

### Boas práticas para DevOps

- **Nunca edite arquivos de sistema sem backup** (especialmente em `/etc`)
- **Use `/opt` para aplicações customizadas** e mantenha controle de versão
- **Monitore o espaço em `/var`** para evitar falhas por falta de disco
- **Limpe `/tmp` regularmente** se não houver política automática
- **Monte discos temporários em `/mnt`** e permanentes via `/etc/fstab`

&nbsp;

### Dicas rápidas de administração

| Comando | Utilidade |
| --- | --- |
| `df -h` | Ver espaço livre em cada partição |
| `du -sh /var/*` | Ver uso de disco por pasta dentro de `/var` |
| `ls -l /dev` | Listar dispositivos reconhecidos pelo sistema |
| `mount` | Ver pontos de montagem ativos |
| `tree -L 1 /` | Visualizar estrutura de diretórios da raiz |

&nbsp;

### Resumo rápido para consultas

- **Configurações** → `/etc`
- **Logs** → `/var/log`
- **Usuários** → `/home` e `/root`
- **Aplicações essenciais** → `/bin`, `/sbin`, `/usr/bin`
- **Bibliotecas** → `/lib`, `/usr/lib`
- **Montagens temporárias** → `/mnt`, `/media`
- **Boot** → `/boot`