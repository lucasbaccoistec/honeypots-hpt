# Plano de testes – Rede de Honeypots (HPT)

- **Tarefa:** 23 – Plano de testes ligado aos requisitos
- **Responsável:** Lucas Bacco
- **Versão:** 1.1 (24/11/2026)

Este plano define os testes que provam que o laboratório cumpre os requisitos da secção 2.2 do relatório. Cada teste tem um identificador (T01 a T19), o requisito que verifica, os passos, o resultado esperado e a evidência a guardar.

## 1. Ambiente de teste

O ambiente é o laboratório descrito no capítulo 3 do relatório:

| Máquina | Rede | Endereço (indicativo) | Papel |
|---|---|---|---|
| VM-ATACANTE (Kali Linux) | Atacante | 10.10.30.10 | Lança os ataques e serve os ficheiros de teste |
| VM-HONEYPOT | Honeynet | 10.10.10.10 | Cowrie, Dionaea e agente Wazuh |
| VM-SIEM | Gestão | 10.10.20.10 | Wazuh (manager, indexer e dashboard) |
| VM-FIREWALL | As três | 10.10.30.254, 10.10.10.254 e 10.10.20.254 | Gateway das três redes; aplica as regras R1 a R8 |
| PC anfitrião | Gestão | 10.10.20.1 | Estação de administração |

Os endereços são os do plano indicativo e devem ser trocados pelos reais quando o Francisco fechar a tarefa 5.

**Ferramentas:** nmap, hydra, clientes SSH, Telnet, FTP e SMB (`smbclient`), `curl` e Metasploit na VM-ATACANTE; o dashboard e as ferramentas de linha de comando do Wazuh na VM-SIEM; `docker` na VM-HONEYPOT; `nft` na VM-FIREWALL.

**Preparar antes de desligar a Internet** (fluxo R8):

- O ficheiro de teste EICAR, descarregado do site oficial [eicar.org](https://www.eicar.org) para uma pasta da VM-ATACANTE. É um ficheiro inofensivo, criado para testar antivírus. Não o guardem no repositório.
- Um servidor web nessa pasta: `sudo python3 -m http.server 80`.
- A imagem de testes de rede `nicolaka/netshoot` na VM-HONEYPOT (`docker pull nicolaka/netshoot`), usada nos testes de isolamento.
- Uma lista curta de palavras-passe (10 a 20) em `lista.txt` na VM-ATACANTE, para os testes de força bruta.

## 2. Regras de execução

1. Antes de cada ronda, criar snapshots das quatro VMs (tarefa 11).
2. Durante os testes, as redes do laboratório não têm Internet. O fluxo R8 tem de estar desligado (placa NAT da VM-FIREWALL com o cabo desligado).
3. Os ataques só partem da VM-ATACANTE e só visam a VM-HONEYPOT, exceto nos testes de isolamento (T15 a T17), que verificam precisamente o que tem de falhar.
4. Cada teste termina com um de três resultados: **Passou**, **Falhou** ou **Bloqueado** (não foi possível executar).
5. As evidências (capturas do dashboard, excertos de logs, resultados de comandos) vão para `tests/resultados/ronda-N/`, com o nome do teste (por exemplo, `T03-cowrie-comandos.png`). Nunca juntar amostras de malware ou ficheiros capturados: só os respetivos hashes.
6. Cada falha dá origem a um cartão no Jira, com o identificador do teste no título (por exemplo, "Falha no T09 – decoder do Dionaea").
7. Depois de qualquer alteração à firewall, às redes ou aos containers, repetir os testes de isolamento (T15 a T18).

## 3. Critérios de entrada e de saída

**Para começar uma ronda** têm de estar concluídas as tarefas 9 (regras de isolamento na VM-FIREWALL), 14 (isolamento dos containers), 17 (agente Wazuh), 19 (dashboards), 20 (fluxo de ponta a ponta) e 39 (VM-ATACANTE).

**A ronda é aceite quando:**

- todos os testes de prioridade alta passam;
- nenhum teste de isolamento (T15 a T18) falha – uma falha aqui suspende os ataques até ser corrigida;
- as falhas de prioridade média estão registadas no Jira, com a correção planeada.

## 4. Resumo dos testes

| ID | Teste | Requisito | Prioridade | Executa | Tarefa |
|---|---|---|---|---|---|
| T01 | Descoberta de serviços | RF1 | Média | Guilherme | 24 |
| T02 | Força bruta SSH | RF1 | Alta | Guilherme | 24 |
| T03 | Login e comandos no Cowrie | RF1 | Alta | Guilherme | 24 |
| T04 | Descarga de ficheiro pelo Cowrie | RF1 | Alta | Guilherme | 24 |
| T05 | Tentativas por Telnet | RF1 | Média | Guilherme | 24 |
| T06 | Serviços do Dionaea (SMB, HTTP, FTP) | RF1 | Alta | Guilherme | 24 |
| T07 | Tempo até ao alerta | RF1 | Média | Guilherme | 24 |
| T08 | Agente ativo | RF2 | Alta | Lucas | 27 |
| T09 | Descodificação dos eventos | RF2 | Alta | Lucas | 27 |
| T10 | Dashboards | RF3 | Alta | Lucas | 27 |
| T11 | Importação de datasets | RF4 | Alta | Guilherme | 45 |
| T12 | Exportação de IoCs | Objetivo 1.2 | Alta | Guilherme | 21 |
| T13 | Consumo de recursos | RNF1 | Média | Guilherme | 26 |
| T14 | Reposição de sensores e de VMs | RNF2 | Média | Guilherme | 26 |
| T15 | Saída da honeynet | RS1 | Alta | Francisco | 25 |
| T16 | Rede atacante | RS1 | Alta | Francisco | 25 |
| T17 | Administração e Internet | RS1 | Alta | Francisco | 25 |
| T18 | Configuração dos containers | RS1 | Alta | Francisco | 25 |
| T19 | Amostras fora do repositório | RS1 | Média | Lucas | 27 |

O T18 é executado pelo Francisco e não pelo Guilherme, que configurou os containers: assim, quem verifica não é quem configurou.

## 5. Casos de teste

### RF1 – Captura de ataques

#### T01 – Descoberta de serviços

- **Objetivo:** confirmar que a VM-ATACANTE só vê os serviços emulados.
- **Passos:** na VM-ATACANTE, correr `nmap -sV -p- 10.10.10.10`.
- **Resultado esperado:** aparecem as portas 22 e 23 (Cowrie) e as portas do Dionaea (secção 3.6 do relatório). A porta SSH de administração da VM-HONEYPOT **não** aparece. O Dionaea regista as ligações em `dionaea.json`.
- **Evidência:** resultado do nmap e excerto de `dionaea.json`.

#### T02 – Força bruta SSH

- **Objetivo:** confirmar que as tentativas de autenticação e as credenciais são registadas.
- **Passos:** `hydra -l root -P lista.txt -t 4 ssh://10.10.10.10`.
- **Resultado esperado:** um evento `cowrie.login.failed` por tentativa falhada, com o utilizador e a palavra-passe, e os alertas correspondentes no Wazuh, com o IP de origem da VM-ATACANTE.
- **Evidência:** excerto de `cowrie.json` e captura dos alertas no Wazuh.

#### T03 – Login e comandos no Cowrie

- **Objetivo:** confirmar que a sessão e os comandos ficam registados.
- **Passos:** `ssh root@10.10.10.10`, com uma palavra-passe aceite pelo `userdb.txt` configurado. Dentro da sessão, correr `uname -a`, `cat /etc/passwd` e `exit`.
- **Resultado esperado:** um evento `cowrie.login.success` e um `cowrie.command.input` por comando. A sessão fica gravada e pode ser revista com o `playlog` do Cowrie.
- **Evidência:** excerto de `cowrie.json` e captura dos alertas no Wazuh.

#### T04 – Descarga de ficheiro pelo Cowrie

- **Objetivo:** confirmar a captura de ficheiros e o cálculo do hash (fluxo R5).
- **Passos:** numa sessão como a do T03, correr `wget http://10.10.30.10/eicar.com`. Na VM-ATACANTE, calcular o hash do original com `sha256sum eicar.com`.
- **Resultado esperado:** um evento `cowrie.session.file_download` com o SHA-256 igual ao calculado na VM-ATACANTE, e o ficheiro guardado no volume de downloads do Cowrie.
- **Evidência:** excerto de `cowrie.json`, os dois hashes e o alerta no Wazuh.

#### T05 – Tentativas por Telnet

- **Objetivo:** confirmar que o Telnet também é registado.
- **Passos:** `telnet 10.10.10.10 23` e três tentativas de login com credenciais diferentes.
- **Resultado esperado:** eventos de autenticação com o protocolo Telnet identificado, e os alertas no Wazuh.
- **Evidência:** excerto de `cowrie.json`.

#### T06 – Serviços do Dionaea (SMB, HTTP, FTP)

- **Objetivo:** confirmar que o Dionaea regista as interações com os serviços emulados.
- **Passos:**
  1. SMB: `smbclient -L //10.10.10.10 -N` e o módulo `auxiliary/scanner/smb/smb_version` do Metasploit.
  2. HTTP: `curl -v http://10.10.10.10/`.
  3. FTP: `ftp 10.10.10.10`, com login anónimo.
- **Resultado esperado:** cada interação aparece em `dionaea.json`, com o protocolo, a porta e o IP de origem, e chega ao Wazuh como alerta.
- **Evidência:** excertos de `dionaea.json` e captura dos alertas.

#### T07 – Tempo até ao alerta

- **Objetivo:** verificar o requisito "em tempo real".
- **Passos:** anotar a hora exata (`date`) na VM-ATACANTE, fazer um login falhado por SSH e procurar o alerta no Wazuh.
- **Resultado esperado:** o alerta aparece no dashboard em menos de 60 segundos. Este limite é uma proposta do grupo para o requisito "em tempo real".
- **Evidência:** a hora anotada e a captura do alerta, com a respetiva hora.

### RF2 – Encaminhamento para o SIEM

#### T08 – Agente ativo

- **Passos:** na VM-SIEM, correr `/var/ossec/bin/agent_control -l`; no dashboard, abrir a lista de agentes.
- **Resultado esperado:** o agente da VM-HONEYPOT aparece como ativo nos dois sítios.
- **Evidência:** resultado do comando e captura do dashboard.

#### T09 – Descodificação dos eventos

- **Passos:**
  1. Na VM-SIEM, passar linhas reais de `cowrie.json` e de `dionaea.json` pelo `/var/ossec/bin/wazuh-logtest`.
  2. No dashboard, abrir os alertas gerados nos testes T02 a T06.
- **Resultado esperado:** os decoders extraem os campos certos (IP de origem no campo `srcip`, porta, utilizador, palavra-passe, comando e hash) e cada tipo de evento tem o nível de severidade definido nas regras.
- **Evidência:** resultado do `wazuh-logtest` e captura de um alerta de cada tipo.

### RF3 – Dashboards

#### T10 – Dashboards

- **Passos:** depois dos testes T01 a T06 e da importação de um dataset (T11), abrir os dashboards.
- **Resultado esperado:**
  - as portas, as credenciais e os comandos mais frequentes correspondem aos ataques feitos (por exemplo, `root` como utilizador mais tentado após o T02);
  - o mapa mostra a origem dos eventos dos datasets com IPs públicos;
  - os ataques simulados aparecem sem localização, porque usam IPs privados.
- **Evidência:** capturas de cada dashboard.

### RF4 – Importação de datasets

#### T11 – Importação de datasets

- **Passos:** importar uma amostra de um dataset (por exemplo, 1 000 eventos) com o script da tarefa 45.
- **Resultado esperado:**
  - o número de eventos no Wazuh, filtrado pelo campo de origem definido no script, é igual ao da amostra;
  - cada evento mantém a data original no campo próprio;
  - os eventos dos datasets distinguem-se dos ataques simulados pelo campo de origem.
- **Evidência:** contagem no ficheiro e contagem no Wazuh.

### Objetivo 1.2 – Indicadores de compromisso

#### T12 – Exportação de IoCs

- **Passos:** correr o script de exportação (tarefa 21) depois dos testes T02 a T06 e T11.
- **Resultado esperado:** o CSV e o JSON incluem o IP da VM-ATACANTE (origem "simulado"), as credenciais do T02, o SHA-256 do ficheiro do T04 e indicadores dos datasets (origem "dataset"), com o número de eventos e as datas da primeira e da última ocorrência.
- **Evidência:** os dois ficheiros exportados. Ficam em `data/exports/`, fora do Git; na pasta de resultados guarda-se apenas um excerto.

### RNF1 e RNF2 – Recursos e reposição

#### T13 – Consumo de recursos

- **Passos:** durante o T02, correr `docker stats --no-stream` na VM-HONEYPOT e ver a memória livre no PC anfitrião.
- **Resultado esperado:** nenhum container ultrapassa os limites definidos na tarefa 14 nem é terminado por falta de memória, e o PC anfitrião mantém pelo menos 2 GB de RAM livres.
- **Evidência:** resultado do `docker stats` e captura da memória do PC anfitrião.

#### T14 – Reposição de sensores e de VMs

- **Passos:**
  1. Contar os eventos já recebidos no Wazuh.
  2. Na VM-HONEYPOT, correr `docker compose down` e depois `docker compose up -d`.
  3. Repetir um login falhado por SSH.
  4. Restaurar o snapshot da VM-HONEYPOT e repetir o passo 3.
- **Resultado esperado:** os sensores voltam a responder em menos de 5 minutos, os eventos anteriores continuam no Wazuh e os novos eventos chegam normalmente.
- **Evidência:** tempos medidos e capturas antes e depois.

### RS1 – Isolamento

#### T15 – Saída da honeynet (R2, R5, R7)

- **Passos:**
  1. A partir de um container de teste na rede Docker dos sensores (`docker run --rm -it --network <rede dos sensores> nicolaka/netshoot`):
     - `curl -m 5 http://1.1.1.1` (Internet);
     - `nc -z -w 5 <IP do router da rede local> 80` (rede local);
     - `nc -z -w 5 10.10.20.10 1514` (VM-SIEM);
     - `curl -m 5 http://10.10.30.10/eicar.com -o /dev/null` (servidor web da VM-ATACANTE).
  2. Na própria VM-HONEYPOT:
     - `nc -z -w 5 10.10.20.10 1514` e `nc -z -w 5 10.10.20.10 1515`;
     - `nc -z -w 5 10.10.20.10 443` e `nc -z -w 5 10.10.20.10 22`.
- **Resultado esperado:**
  - No container, só o pedido ao servidor web da VM-ATACANTE (R5) funciona; todos os outros falham.
  - Na VM-HONEYPOT, as portas 1514 e 1515 da VM-SIEM respondem (R2); a 443 e a 22 falham (R7).
- **Evidência:** resultado de cada comando.

#### T16 – Rede atacante (R1, R6)

- **Passos:** na VM-ATACANTE, correr `nmap -sn 10.10.20.0/24` e `nmap -Pn -p 22,443,1514,1515,9200 10.10.20.10`.
- **Resultado esperado:** nenhuma máquina nem porta da rede de gestão responde. O acesso aos serviços emulados da VM-HONEYPOT já foi verificado no T01.
- **Evidência:** resultado dos dois comandos.

#### T17 – Administração e Internet (R3, R4, R8)

- **Passos:**
  1. A partir da estação de administração (PC anfitrião), abrir o dashboard (443) e entrar por SSH na VM-SIEM e na VM-HONEYPOT.
  2. Em cada VM, correr `curl -m 5 http://1.1.1.1`.
  3. Na VM-FIREWALL, correr `sudo nft list ruleset` e confirmar que as cadeias de entrada e de encaminhamento bloqueiam por omissão (`policy drop`).
- **Resultado esperado:** os acessos de administração funcionam (R3 e R4), nenhuma VM chega à Internet durante os testes (R8 desligado) e a VM-FIREWALL bloqueia por omissão tudo o que não está na Tabela 6.
- **Evidência:** capturas dos acessos, resultado dos comandos e excerto das regras da VM-FIREWALL.

#### T18 – Configuração dos containers

- **Passos:** na VM-HONEYPOT, correr `docker inspect` em cada container e verificar os campos `Privileged`, `User`, `CapDrop`, os limites de memória e de CPU e os volumes montados.
- **Resultado esperado:** nenhum container é privilegiado, todos correm com um utilizador sem privilégios, têm as capacidades reduzidas e os limites definidos na tarefa 14, e nenhum monta o socket do Docker (`/var/run/docker.sock`).
- **Evidência:** excerto do `docker inspect` de cada container.

#### T19 – Amostras fora do repositório

- **Passos:** no repositório, correr `git ls-files` e `git check-ignore -v` sobre as pastas de downloads e de binários dos sensores.
- **Resultado esperado:** nenhum ficheiro capturado, log, dataset em bruto ou ficheiro `.env` está versionado, e as pastas dos sensores são ignoradas pelo Git.
- **Evidência:** resultado dos dois comandos.

## 6. Rastreabilidade

| Requisito | Testes |
|---|---|
| RF1 – Capturar e registar ataques em tempo real | T01 a T07 |
| RF2 – Encaminhar os logs para o SIEM | T08 e T09 |
| RF3 – Dashboards com origem, portas e credenciais | T10 |
| RF4 – Importar datasets públicos | T11 |
| Objetivo 1.2 – Produzir IoCs | T12 |
| RNF1 – Baixo impacto de recursos | T13 |
| RNF2 – Replicação e reinício | T14 |
| RS1 – Isolamento estrito do laboratório | T15 a T19 |

Os resultados de cada ronda são registados com o modelo em `tests/resultados/modelo-ronda.md` e resumidos no capítulo 5 do relatório.
