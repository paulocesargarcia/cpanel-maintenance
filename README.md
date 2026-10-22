# cPanel Maintenance

Automacao de manutencao de servidores cPanel/WHM com CloudLinux/AlmaLinux utilizando Ansible a partir do computador do administrador.

**Privacidade:** o repositorio nao armazena inventarios de servidores, senhas, chaves SSH nem configuracoes especificas dos hosts. O inventario e fornecido localmente por `-i`.

## Execucao completa (exemplo vps3015)

No computador local, com Ansible instalado e acesso SSH por chave:

```bash
git clone git@github.com:paulocesargarcia/cpanel-maintenance.git
cd cpanel-maintenance
ansible-playbook -i 'vps3015.maxdominios.com,' -u root -e 'ansible_port=22022' playbooks/maintenance.yml --syntax-check
ansible all -i 'vps3015.maxdominios.com,' -u root -e 'ansible_port=22022' -m ping
ansible-playbook -i 'vps3015.maxdominios.com,' -u root -e 'ansible_port=22022' playbooks/maintenance.yml
```

Para uma instalacao ja clonada, revise as alteracoes antes de `git pull --ff-only`. A virgula no inventario inline e obrigatoria.

O playbook valida a plataforma e o cPanel, executa `upcp`, atualiza pacotes com DNF, tenta atualizar regras ModSecurity do Imunify360, limpa o cache DNF e verifica reboot/kernel. **Nao executa reboot automaticamente.** Atualizacoes podem reiniciar servicos: use janela de manutencao e confirme backups.

O `ansible.cfg` define `interpreter_python = auto_silent`: usa descoberta automatica do interpretador compativel de cada servidor, sem fixar a versao nem emitir o aviso de descoberta.

**Leia:** [Guia de execucao](docs/EXECUCAO.md).
