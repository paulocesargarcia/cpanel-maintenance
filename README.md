# cPanel Maintenance

Manutencao de servidores cPanel/WHM com Ansible local. **Inventarios, senhas, chaves e relatorios nunca sao versionados.**

Padroes definidos no `ansible.cfg`: `root`, SSH `22022`, descoberta automatica do Python.

## Auditoria sem alteracoes remotas

```bash
ansible-playbook -i 'lab01,' playbooks/audit.yml
```

## Manutencao completa

```bash
ansible-playbook -i 'lab01,' playbooks/maintenance.yml
```

A virgula no inventario inline e obrigatoria. Rode da raiz do repositorio para carregar `ansible.cfg`.

Funcionalidades: atualizacao DNF do sistema (incluindo CloudLinux quando presente), seguida da atualizacao cPanel/WHM, regras ModSecurity Imunify360, verificacao de servicos e kernel/reboot, e relatorio JSON por host em `reports/` **localmente**. O playbook nao reinicia servidores automaticamente. DNF e upcp podem reiniciar servicos: execute em janela de manutencao.

[Guia de execucao](docs/EXECUCAO.md).

## Compatibilidade AlmaLinux/CloudLinux 8

A selecao do Python remoto e feita por `raw` antes da coleta de fatos. Se o host possuir apenas Python 3.6, a execucao sera bloqueada; instale separadamente Python 3.11 sem substituir o interpretador do sistema. No EL8 o DNF e executado pelo comando nativo `/usr/bin/dnf upgrade -y` para preservar a compatibilidade com suas bibliotecas. EL9+ continua usando `ansible.builtin.dnf`. Veja [EXECUCAO.md](docs/EXECUCAO.md).
