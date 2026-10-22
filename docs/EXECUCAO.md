# Execucao local com Ansible

## Modelo de acesso

O repositorio contem apenas automacao e documentacao. Nao ha inventario versionado. O comando `-i 'hostname,'` informa os servidores em tempo de execucao. Use chaves SSH existentes na sua maquina e valide a identidade SSH dos hosts.

Aliases definidos somente em `~/.zshrc` (por exemplo, `alias vps3015="ssh -p22022 root@vps3015.maxdominios.com"`) **nao sao interpretados pelo Ansible**. Use hostname/porta/usuario no comando ou configure o alias em `~/.ssh/config`.

## Pre-requisitos

- Ansible no computador de administracao (macOS/Linux).
- Chave SSH autorizada no servidor; usuario root ou sudo disponivel.
- Python compativel no servidor para os modulos Ansible.
- Backups validos, avaliacao das atualizacoes e janela de manutencao. O DNF e o upcp podem reiniciar servicos.
- Executar dentro da raiz do repositorio para carregar o `ansible.cfg`.

## Clonar e atualizar o projeto

```bash
git clone git@github.com:paulocesargarcia/cpanel-maintenance.git
cd cpanel-maintenance
git status
git pull --ff-only
```

Antes de executar em producao, revise alteracoes do projeto e prefira um commit/tag testado.

## Teste inicial no vps3015

```bash
ansible --version
ansible-playbook -i 'vps3015.maxdominios.com,' -u root -e 'ansible_port=22022' playbooks/maintenance.yml --syntax-check
ansible all -i 'vps3015.maxdominios.com,' -u root -e 'ansible_port=22022' -m ping
```

A virgula apos o hostname e obrigatoria em inventarios inline. `pong` valida SSH/autenticacao/Python remoto, mas nao confirma sucesso das atualizacoes.

## Executar toda a manutencao

```bash
ansible-playbook \
  -i 'vps3015.maxdominios.com,' \
  -u root \
  -e 'ansible_port=22022' \
  playbooks/maintenance.yml
```

Execute somente em janela apropriada: o playbook **faz atualizacoes reais** de cPanel e pacotes. Nao reinicia automaticamente o servidor.

## Apenas uma etapa

As tags disponiveis sao `cpanel`, `system`, `imunify`, `cleanup` e `reboot-check`.

```bash
ansible-playbook -i 'vps3015.maxdominios.com,' -u root -e 'ansible_port=22022' playbooks/maintenance.yml --tags reboot-check
```

As validacoes iniciais sem tags nao sao executadas quando voce seleciona somente uma tag; use a execucao completa para a manutencao padrao.

## Diversos servidores sem inventario versionado

```bash
ansible-playbook -i 'ns1003.maxdominios.com,ns1009.maxdominios.com,' -u root -e 'ansible_port=22022' playbooks/maintenance.yml
```

O playbook usa `serial: 1` e `any_errors_fatal: true`: atualiza um servidor por vez e para em caso de erro. O exemplo pressupoe a mesma porta/usuario nos dois destinos.

Opcionalmente, guarde um inventario **somente na maquina local**, fora do repositorio, e indique seu caminho com `-i`.

## Python

O arquivo `ansible.cfg` define `interpreter_python = auto_silent`. O Ansible identifica um Python remoto compativel para cada host; nao e necessariamente a versao mais nova. Isso elimina apenas o aviso informativo de descoberta, nao erros reais.

## Validacao apos execucao

Analise o resumo `PLAY RECAP`, a saida da tarefa de reboot e a comparacao entre kernel em uso e kernel padrao de boot. `dnf needs-restarting -r` pode nao estar disponivel em todas as instalacoes; nesse caso a avaliacao de reboot fica limitada. Consulte tambem o estado dos servicos criticos cPanel, web, banco e e-mail antes de considerar a manutencao concluida.

O parametro `--check` nao simula de forma confiavel tarefas imperativas como `upcp` e atualizacao de regras, portanto nao substitui o teste em ambiente controlado.
