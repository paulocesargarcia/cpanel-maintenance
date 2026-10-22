# cPanel Maintenance

Automacao de manutencao de servidores cPanel/WHM com CloudLinux/AlmaLinux usando Ansible na estacao local.

O repositorio **nao contem inventario, chaves SSH nem credenciais**. O inventario e informado somente na execucao com `-i`.

## Padroes de conexao

No `ansible.cfg`, `remote_user = root`, `remote_port = 22022` e `interpreter_python = auto_silent`. A autenticacao usa a chave SSH disponivel na estacao local. Os valores podem ser sobrescritos por host quando necessario.

## Teste e execucao completa

```bash
git clone git@github.com:paulocesargarcia/cpanel-maintenance.git
cd cpanel-maintenance
ansible-playbook -i 'vps3015.maxdominios.com,' playbooks/maintenance.yml --syntax-check
ansible all -i 'vps3015.maxdominios.com,' -m ping
ansible-playbook -i 'vps3015.maxdominios.com,' playbooks/maintenance.yml
```

A virgula no inventario inline e obrigatoria. Em instalacoes existentes, revise as alteracoes e utilize `git pull --ff-only`.

O playbook valida a plataforma e o cPanel, executa `upcp`, atualiza pacotes DNF, regras ModSecurity do Imunify360, limpa o cache e verifica necessidade de reboot. **Nao reinicia automaticamente**, mas atualizacoes podem reiniciar servicos. Use janela de manutencao e confirme backups.

Leia [a documentacao de execucao](docs/EXECUCAO.md).
