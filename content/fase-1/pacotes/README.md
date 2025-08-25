# Gerenciamento de Pacotes no Linux (Essencial para DevOps)

<details>
<summary class="summary">Sumário</summary>

- [O que é gerenciamento de pacotes](#o-que-é-gerenciamento-de-pacotes)
- [Ferramentas no Ubuntu/Debian](#ferramentas-no-ubuntudebian)
- [Comandos práticos do APT](#comandos-práticos-do-apt)
- [Exemplos úteis no dia a dia](#exemplos-úteis-no-dia-a-dia)
- [Outros gerenciadores de pacotes](#outros-gerenciadores-de-pacotes)
- [Boas práticas DevOps](#boas-práticas-devops)
- [Resumo rápido](#resumo-rápido)
</details>


> ## O que é gerenciamento de pacotes

O gerenciamento de pacotes é o processo de **instalar, atualizar, remover e administrar softwares** em distribuições Linux.

### Elementos principais:

- **Pacotes** → arquivos contendo softwares já compilados (ex: `.deb` no Debian/Ubuntu, `.rpm` no RHEL/Fedora).
- **Repositórios** → servidores online com pacotes e dependências.
- **Gerenciadores de pacotes** → ferramentas que automatizam a instalação, atualização e remoção de pacotes.

---

> ## Ferramentas no Ubuntu/Debian

- **APT (Advanced Package Tool)** → interface de alto nível para lidar com pacotes e dependências.
- **dpkg** → gerenciador de baixo nível, usado para instalar diretamente arquivos `.deb`.

---

> ## Comandos práticos do APT

### Atualização do sistema

```bash
sudo apt update              # Atualiza a lista de pacotes disponíveis
sudo apt upgrade             # Atualiza pacotes instalados
sudo apt full-upgrade        # Atualiza e remove pacotes obsoletos
```

### Instalação de pacotes

```bash
sudo apt install nome-do-pacote
sudo apt install -y nome-do-pacote   # Sem confirmação
sudo dpkg -i pacote.deb              # Instala pacote .deb local
```

### Pesquisa e informações

```bash
apt search nome-do-pacote    # Pesquisa pacotes disponíveis
apt show nome-do-pacote      # Exibe detalhes
dpkg -l                      # Lista pacotes instalados
```

### Remoção de pacotes

```bash
sudo apt remove nome-do-pacote   # Remove, mantém configs
sudo apt purge nome-do-pacote    # Remove junto com configs
sudo apt autoremove              # Remove dependências não usadas
```

### Verificação e manutenção

```bash
sudo apt list --upgradable       # Lista pacotes atualizáveis
sudo apt clean                   # Limpa cache de pacotes baixados
sudo apt autoclean               # Remove arquivos antigos
dpkg -L nome-do-pacote           # Lista arquivos de um pacote
```

---

> ## Exemplos úteis no dia a dia

### Instalar utilitários comuns:

```bash
sudo apt install curl wget git vim
```

### Reinstalar pacote corrompido:

```bash
sudo apt --reinstall install nome-do-pacote
```

### Corrigir dependências quebradas:

```bash
sudo apt --fix-broken install
```

---

> ## Outros gerenciadores de pacotes

### Fedora/RHEL (DNF/YUM):

```bash
sudo dnf install nome-do-pacote
sudo dnf update
sudo dnf remove nome-do-pacote
```

### Arch Linux (Pacman):

```bash
sudo pacman -Syu nome-do-pacote      # Instala/atualiza
sudo pacman -Rns nome-do-pacote      # Remove com dependências
```

---

> ## Boas práticas DevOps

| Prática | Benefício |
| --- | --- |
| Usar `apt update && apt upgrade` regularmente | Mantém servidor atualizado |
| Sempre revisar pacotes antes do `full-upgrade` | Evita remoção inesperada |
| Utilizar `autoremove` | Mantém o sistema limpo |
| Repositórios oficiais + PPAs confiáveis | Reduz riscos de segurança |
| Automatizar updates em servidores críticos com Ansible ou scripts | Padroniza ambientes |

---

> ## Resumo rápido

- **APT** → alto nível (instala, atualiza, remove, resolve dependências).
- **dpkg** → baixo nível (instala pacotes `.deb`).
- **Comandos principais**: `apt install`, `apt update`, `apt upgrade`, `apt remove`, `apt purge`, `apt autoremove`.
- **Manutenção**: `apt clean`, `apt autoclean`, `apt --fix-broken install`.
- **Boas práticas**: manter sistema atualizado, usar repositórios confiáveis, automatizar com ferramentas de configuração.
