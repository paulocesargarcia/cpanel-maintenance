# Execucao local

Nao existe inventario versionado. O Ansible roda na sua maquina com chaves SSH locais. O arquivo `ansible.cfg` define `remote_user=root`, `remote_port=22022`, `interpreter_python=auto_silent` e verifica chaves SSH.

```bash
git pull --ff-only
ansible-playbook -i 'vps3015.maxdominios.com,' playbooks/audit.yml --syntax-check
ansible-playbook -i 'vps3015.maxdominios.com,' playbooks/audit.yml
```

A auditoria **nao instala nem atualiza** pacotes, nao modifica configuracoes remotas e nao reinicia servicos. Requer root para descobrir servicos/estado do sistema. Para executar atualizacoes reais, com backups e janela de manutencao:

```bash
ansible-playbook -i 'vps3015.maxdominios.com,' playbooks/maintenance.yml
```

Etapas da manutencao: validar cPanel e sistema; coletar servicos antes; identificar CloudLinux e Imunify360; executar upcp; atualizar DNF incluindo pacotes CloudLinux presentes; atualizar regras ModSecurity do Imunify360 quando instalado; limpar cache DNF; coletar servicos depois; verificar reboot/kernel; gerar relatorio local.

Os pacotes CloudLinux sao atualizados por DNF conforme repositorios configurados. Nao se executa conversao de SO, `cagefsctl --force-update` nem alteracoes estruturais. Imunify360 ausente e ignorado. Um erro em atualizacao interrompe as etapas seguintes e e registrado no relatorio.

## Relatorios

Gerados no controlador em `reports/HOST-DATA.json`, com permissao `0600`. O diretorio tem permissao `0700` e esta no `.gitignore`. Contem status de execucao, CloudLinux, Imunify360, kernel/reboot e estados de servicos antes/depois.

Os servicos verificados por coleta de fatos incluem cPanel, Apache, LiteSpeed, MariaDB/MySQL, Exim, Dovecot e Imunify360, quando detectados. Nao reinicia ou repara servicos automaticamente.

O retorno do reboot e interpretado como 0 (nao indicado), 1 (indicado), demais (inconclusivo). Compare tambem kernels e avalie manualmente necessidade de reinicio. O campo de kernel de boot retorna caminho completo.

## Requisitos e seguranca

Ansible com colecao builtin, acesso SSH por chave e Python compatível nos hosts. O playbook usa `serial: 1` e `any_errors_fatal: true`. Nunca execute atualizacoes sem revisar os commits. Nenhum reboot automatico esta implementado.

Alias do ZSH nao e interpretado como alias SSH. Use nome DNS ou defina `Host` no seu `~/.ssh/config`.
