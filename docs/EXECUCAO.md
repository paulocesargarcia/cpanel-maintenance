# Execucao local com Ansible (sem inventario no GitHub)

O GitHub armazena somente os playbooks/documentacao. Os nomes, IPs, credenciais e aliases SSH permanecem na maquina de administracao.

## Pre-requisitos

- Ansible instalado na maquina controladora (macOS/Linux).
- Alias SSH valido em `~/.ssh/config`, com chave privada mantida localmente.
- Usuario remoto root, ou sudo/become configurado apropriadamente.
- Python 3 disponivel nos servidores (necessario aos modulos Ansible).
- Janela de manutencao: atualizacoes podem reiniciar servicos web, banco de dados etc.

## Instalar/clonar

```bash
git clone git@github.com:paulocesargarcia/cpanel-maintenance.git
cd cpanel-maintenance
ansible --version
ssh ns1003 'id -u'
```

## Testar conectividade

```bash
ansible all -i 'ns1003,' -m ping
```

A virgula e obrigatoria: ela informa ao Ansible que `ns1003` e um host inline, nao um nome de arquivo.

## Executar no servidor ns1003

```bash
ansible-playbook -i 'ns1003,' playbooks/maintenance.yml
```

Os aliases do SSH local sao respeitados. Caso SSH conecte com usuario sem sudo automatico, configure o `become` no Ansible local, preferencialmente fora do repositório.

## Executar em dois servidores, sequencialmente

```bash
ansible-playbook -i 'ns1003,ns1009,' playbooks/maintenance.yml
```

O playbook usa `serial: 1`, nunca processa mais de um host por vez, e interrompe o restante em caso de erro.

## Inventario local opcional (fora do repositorio)

Crie `~/.config/cpanel-maintenance/hosts.ini`:

```ini
[cpanel]
ns1003
ns1009
```

Depois:

```bash
ansible-playbook -i ~/.config/cpanel-maintenance/hosts.ini playbooks/maintenance.yml --limit ns1003
```

Nao armazene o inventario nem credenciais no repositorio.

## Atualizar arquivos do projeto

```bash
git pull --ff-only
ansible-playbook -i 'ns1003,' playbooks/maintenance.yml --syntax-check
```

Revisar commits e executar revisoes aprovadas/tags estaveis antes de realizar manutencao em producao.

## Etapas e observacoes

1. Valida plataforma e presenca do cPanel.
2. Executa `upcp` (sem `--force`).
3. Atualiza pacotes via DNF, incluindo aqueles fornecidos pelos repositorios configurados.
4. Atualiza regras ModSecurity do Imunify360, se instalado.
5. Limpa cache DNF.
6. Avalia reboot e exibe kernel em uso e de boot. **Nao reinicia automaticamente**.

As tarefas `command` de atualizacao nao oferecem simulacao confiavel com `--check`. O resultado de `needs-restarting` pode variar com a distribuicao e as atualizacoes; nao substitui a verificacao do kernel e dos servicos. Sem `needs-restarting` o resumo indicara que nao foi avaliado.
