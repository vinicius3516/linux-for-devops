# Permissões de Arquivos e Diretórios no Linux

<details>
<summary class="summary">Sumário</summary>

- [1. Conceito geral](#conceito-geral)
- [2. Interpretação de Permissões](#interpretacao-de-permissoes)
- [3. Alterando Permissões (chmod)](#alterando-permissoes-chmod)
- [4. Alterando Owner e Group (chown/chgrp)](#alterando-owner-e-group-chownchgrp)
- [5. Permissões Especiais](#permissoes-especiais)
- [6. Umask (Permissões Padrão)](#umask-permissoes-padrao)
- [7. Boas Práticas DevOps](#boas-praticas-devops)
- [8. Dicas rápidas](#dicas-rapidas)
- [9. Alterando as configurações padrão do Umask no Ubuntu](#alterando-as-configuracoes-padrao-do-umask-no-ubuntu)
- [10. Resumo rápido](#resumo-rapido)
</details>

### Conceito Geral

No Linux, **todo arquivo ou diretório tem**:

- **Owner** (*usuário dono*)
- **Group owner** (*grupo dono*)
- **Others** (*todos que não são owner nem membros do grupo*)

Cada um desses pode ter permissões distintas, definidas para três ações básicas:

| Permissão | Inglês | Letra | Valor numérico |
| --- | --- | --- | --- |
| Leitura | Read | `r` | 4 |
| Escrita | Write | `w` | 2 |
| Execução | Execute | `x` | 1 |

**Arquivos** → leitura significa ver conteúdo, execução significa rodar como programa/script.

**Diretórios** → leitura significa listar arquivos, execução significa entrar/percorrer, escrita significa criar/apagar/renomear arquivos.

&nbsp;

### Interpretação de Permissões

Exemplo de saída `ls -l`:

```bash
-rw-rw-r-- 1 vagrant vagrant 0 Aug 12 22:21 test.txt
```

Destrinchando:

- `-` Tipo de item (`-` arquivo, `d` diretório, `l` link, etc.)
- `rw-` → Permissões do **owner**
- `rw-` → Permissões do **group**
- `r--` → Permissões dos **others**
- `vagrant` → Usuário dono
- `vagrant` → Grupo dono

Valor numérico:

- Owner: `rw-` → 4 + 2 + 0 = **6**
- Group: `rw-` → 4 + 2 + 0 = **6**
- Others: `r--` → 4 + 0 + 0 = **4**
    
    **Resultado:** `664`

&nbsp;

### Alterando Permissões (chmod)

Modo numérico:

```bash
chmod 777 arquivo.txt
chmod 764 -R pasta/
```
**Recursivo:** `-R` aplica a mudança a todo o conteúdo do diretório.

Modo simbólico:

```bash
chmod u=rwx,g=rw,o=r arquivo.txt   # Define permissões exatas, onde u = owner, g = group, o = others
chmod u+w arquivo.txt              # Adiciona escrita ao owner
chmod g-r arquivo.txt              # Remove leitura do grupo
chmod a+x script.sh                # Adiciona execução para todos
```

&nbsp;

### Alterando Owner e Group (chown/chgrp)

- Alterar **owner**:

```bash
sudo chown usuario arquivo.txt
```

- Alterar **group**:

```bash
sudo chown :grupo arquivo.txt
sudo chgrp grupo arquivo.txt
```

- Alterar **ambos**:

```bash
sudo chown usuario:grupo arquivo.txt
```

- Recursivo:

```bash
sudo chown -R usuario:grupo pasta/
```

&nbsp;

### Permissões Especiais

**SUID** (*Set User ID*)

- Executa com permissões do **owner** do arquivo.
- Indicado por `s` no campo de execução do owner.

```bash
-rwsr-xr-x 1 root root 68208 Feb 6 /usr/bin/passwd
```

**SGID** (*Set Group ID*)

- Em arquivos: executa com permissões do **group owner**.
- Em diretórios: novos arquivos herdam o grupo do diretório.

```bash
drwxr-sr-x 2 vagrant vagrant 4096 Aug 12 dir1
```

**Sticky Bit**

- Usado em diretórios: usuários só podem apagar seus próprios arquivos.
- Indicado por `t` no campo de execução dos others.

```bash
drwxr-sr-t 2 vagrant vagrant 4096 Aug 12 dir1
```

&nbsp;

### Umask (Permissões Padrão)

- Define permissões padrão para novos arquivos/diretórios.
- Padrão:
    - Diretórios → `777`
    - Arquivos → `666`
- Fórmula: `Permissão padrão - umask = permissão final`

Ver e alterar:

```bash
umask        # Ver valor atual
umask 0002   # Alterar temporariamente
```

Exemplo:

- Umask = `0002`
- Diretório novo: `777 - 002 = 775`
- Arquivo novo: `666 - 002 = 664`

&nbsp;

### Boas Práticas DevOps 

| Prática | Benefício |
| --- | --- |
| Usar menor privilégio necessário | Evita brechas de segurança |
| Monitorar permissões sensíveis em `/etc` e `/var` | Protege configs críticas |
| Usar sticky bit em `/tmp` | Evita exclusões indevidas |
| Não dar `777` em produção | Reduz exposição |
| Definir umask adequado | Padroniza segurança |

&nbsp;

### Dicas rápidas

| Comando | O que faz |
| --- | --- |
| `ls -l` | Lista permissões e donos |
| `stat arquivo` | Detalhes do arquivo |
| `chmod` | Altera permissões |
| `chown` | Altera owner e group |
| `umask` | Exibe/define permissões padrão |

&nbsp;

### Alterando as configurações padrão do Umask no Ubuntu

Edite o arquivo padrão de configuração
```bash
sudoedit /etc/login.defs
```

Procure por 'UMASK' e altere, por exemplo, para:

```
UMASK	027
```

ou

```
UMASK	077
```

> 022 → permissivo (arquivos 644 / diretórios 755)
> 
> 
> 027 → mais restrito (arquivos 640 / diretórios 750)
> 
> 077 → bem restrito (arquivos 600 / diretórios 700)
>

&nbsp;

### Resumo rápido

- **Permissões:** leitura (4), escrita (2), execução (1)
- **Ordem:** owner → group → others
- **Alteração:** `chmod`, `chown`, `chgrp`
- **Especiais:** SUID (`s` owner), SGID (`s` group), Sticky Bit (`t` others)
- **Umask:** controla permissões padrão