# cPanel Maintenance

Manutencao de servidores cPanel/WHM com Ansible local. **Inventarios, senhas, chaves e relatorios nunca sao versionados.**

Padroes definidos no `ansible.cfg`: `root`, SSH `22022`, descoberta automatica do Python.

## Auditoria sem alteracoes remotas

```bash
ansible-playbook -i 'vps3015.maxdominios.com,' playbooks/audit.yml
```

## Manutencao completa

```bash
ansible-playbook -i 'vps3015.maxdominios.com,' playbooks/maintenance.yml
```

A virgula no inventario inline e obrigatoria. Rode da raiz do repositorio para carregar `ansible.cfg`.

Funcionalidades: atualizacoes cPanel e DNF (incluindo CloudLinux quando presente), regras ModSecurity Imunify360, verificacao de servicos e kernel/reboot, e relatorio JSON por host em `reports/` **localmente**. O playbook nao reinicia servidores automaticamente. DNF e upcp podem reiniciar servicos: execute em janela de manutencao.

[Guia de execucao](docs/EXECUCAO.md).
