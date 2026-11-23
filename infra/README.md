# Infraestrutura

Responsável: Francisco Soares.

| Pasta | Conteúdo | Tarefas |
|---|---|---|
| `hipervisor/` | Instalação do hipervisor e criação das VMs | 4, 6, 7, 11, 39 |
| `redes/` | Redes virtuais, plano de endereçamento e acesso da equipa | 5, 35 |
| `firewall/` | Regras de isolamento, incluindo a cadeia `DOCKER-USER` | 9 |
| `hardening/` | SSH, utilizadores, NTP e atualizações | 8 |

Plano de endereçamento indicativo (a confirmar na tarefa 5):

| Rede | Endereços | Máquinas |
|---|---|---|
| Atacante | 10.10.30.0/24 | Kali |
| Honeynet | 10.10.10.0/24 | VM-HONEYPOT |
| Gestão | 10.10.20.0/24 | VM-SIEM |

Depois das instalações, nenhuma destas redes tem saída para a Internet nem para a rede local da máquina anfitriã.
