# Infraestrutura

Responsável: Francisco Soares.

| Pasta | Conteúdo | Tarefas |
|---|---|---|
| `hipervisor/` | Instalação do VirtualBox e criação das VMs | 4, 6, 7, 11, 39, 47 |
| `redes/` | Redes virtuais, plano de endereçamento e acesso da equipa | 5, 35 |
| `firewall/` | Regras R1 a R8 da VM-FIREWALL (nftables) e cadeia `DOCKER-USER` da VM-HONEYPOT | 9, 47 |
| `hardening/` | SSH, utilizadores, NTP e atualizações | 8 |

Plano de endereçamento indicativo (a confirmar na tarefa 5):

| Rede | Tipo no VirtualBox | Endereços | Máquinas |
|---|---|---|---|
| Atacante | Rede interna `hpt-atacante` | 10.10.30.0/24 | VM-ATACANTE (.10), VM-FIREWALL (.254) |
| Honeynet | Rede interna `hpt-honeynet` | 10.10.10.0/24 | VM-HONEYPOT (.10), VM-FIREWALL (.254) |
| Gestão | Placa só de anfitrião, sem DHCP | 10.10.20.0/24 | PC anfitrião (.1), VM-SIEM (.10), VM-FIREWALL (.254) |

A VM-FIREWALL é o gateway de cada rede. Durante as instalações tem uma quarta placa, em NAT, que é a única saída para a Internet; fora disso, essa placa fica com o cabo desligado.

Nenhuma VM usa placa em ponte. Depois das instalações, nenhuma destas redes tem saída para a Internet nem para a rede local da máquina anfitriã.
