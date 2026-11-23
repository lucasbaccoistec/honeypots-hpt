# Rede de Honeypots – Projeto Final (CTeSP Cibersegurança, ISTEC Porto)

Laboratório isolado para deteção e análise de tentativas de intrusão com honeypots.
Grupo **Honeypots (HPT)**: Lucas Bacco (líder), Francisco Soares e Guilherme Barros.

## Como funciona

Tudo corre localmente, numa máquina anfitriã do grupo, **sem nada exposto à Internet**:

- **Rede atacante:** uma máquina Kali simula a Internet e lança ataques controlados.
- **Rede honeynet:** a VM-HONEYPOT corre, em Docker, os iscos **Cowrie** (SSH/Telnet) e **Dionaea** (SMB, HTTP, FTP…), mais o agente **Wazuh**.
- **Rede de gestão:** a VM-SIEM corre o **Wazuh 4.14** all-in-one (manager, indexer e dashboard).
- **Datasets públicos:** ataques reais recolhidos por outros honeypots, convertidos e importados no Wazuh para análise.

A decisão de trabalhar só em laboratório está registada em [docs/decisoes/0001-laboratorio-local.md](docs/decisoes/0001-laboratorio-local.md).

## Estrutura

| Pasta | Conteúdo | Tarefas | Responsável |
|---|---|---|---|
| `infra/` | Hipervisor, redes virtuais, regras de isolamento e hardening | 4–9, 11, 35, 39 | Francisco |
| `sensors/` | Cowrie e Dionaea em Docker | 12–14, 36 | Guilherme |
| `siem/` | Wazuh: decoders, regras, dashboards e agente | 15–19 | Lucas (agente: Guilherme) |
| `datasets/` | Escolha, conversão e importação dos datasets públicos | 44–45 | Guilherme |
| `scripts/` | Exportação de IoCs | 21–22 | Guilherme |
| `tests/` | Plano de testes, ataques simulados e resultados | 23–27 | Todos |
| `analise/` | Análise dos dados recolhidos e dos datasets | 29 | Guilherme e Lucas |
| `docs/` | Relatório, diagramas, decisões e manual | 30–33, 42, 43, 46 | Todos |

Cada pasta tem um `README.md` com o que lá deve ficar.

## Regras de ouro

1. **Nada exposto à Internet:** os iscos só são alcançáveis a partir da rede atacante do laboratório.
2. **Nunca fazer commit de segredos:** palavras-passe, chaves, `.env` ou credenciais do Wazuh.
3. **Nunca fazer commit de malware** nem de ficheiros capturados pelos iscos: só os hashes SHA-256.
4. **Os datasets em bruto e as imagens das VMs ficam fora do Git** (ver `.gitignore`).

## Como trabalhar

Ver [CONTRIBUTING.md](CONTRIBUTING.md).
