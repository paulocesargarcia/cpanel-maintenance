# cPanel Maintenance

Playbook Ansible para manutencao de servidores cPanel/WHM com CloudLinux/AlmaLinux.

**Principio:** nenhum inventario, IP, chave SSH ou credencial fica neste repositorio. A execucao e **local**, aproveitando os aliases do `~/.ssh/config`.

## Executar

```bash
git clone git@github.com:paulocesargarcia/cpanel-maintenance.git
cd cpanel-maintenance
ansible all -i 'ns1003,' -m ping
ansible-playbook -i 'ns1003,' playbooks/maintenance.yml
```

A virgula apos `ns1003` e obrigatoria para inventario inline. O SSH deve estar configurado localmente, com permissao de root/sudo.

O playbook executa, em sequencia, `upcp`, atualizacao DNF, atualizacao das regras ModSecurity do Imunify360, limpeza do cache e verificacao de necessidade de reboot. **Nao reinicia o servidor.** Atualizacoes podem reiniciar servicos, portanto use janela de manutencao.

Documentacao: [docs/EXECUCAO.md](docs/EXECUCAO.md).
