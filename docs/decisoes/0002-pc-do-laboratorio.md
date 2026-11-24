# 0002 – PC de uso diário: VirtualBox e VM-FIREWALL

- **Data:** 2026-11-24
- **Estado:** aceite pelo grupo
- **Tarefa:** 34 – Confirmar o PC do laboratório

## Contexto

O laboratório corre no PC de um elemento do grupo, que é de uso diário. O relatório previa o Proxmox VE, se o PC pudesse ficar dedicado ao laboratório, ou o VirtualBox. No desenho inicial, era o hipervisor que aplicava as regras R1 a R8 entre as três redes.

## Decisão

- O hipervisor é o **VirtualBox**, instalado sobre o sistema do PC. O Proxmox VE substituiria esse sistema, o que não é possível num PC de uso diário.
- O VirtualBox isola as redes internas, mas não filtra o tráfego entre elas. Por isso, o laboratório passa a ter uma quarta VM, a **VM-FIREWALL**: Ubuntu Server com nftables, 1 vCPU, 1,5 GB de RAM e 10 GB de disco. É o gateway das três redes e aplica as regras R1 a R8.
- As redes no VirtualBox ficam assim:

| Rede | Tipo no VirtualBox | Endereços | Máquinas |
|---|---|---|---|
| Atacante | Rede interna `hpt-atacante` | 10.10.30.0/24 | VM-ATACANTE (.10), VM-FIREWALL (.254) |
| Honeynet | Rede interna `hpt-honeynet` | 10.10.10.0/24 | VM-HONEYPOT (.10), VM-FIREWALL (.254) |
| Gestão | Placa só de anfitrião, sem DHCP | 10.10.20.0/24 | PC anfitrião (.1), VM-SIEM (.10), VM-FIREWALL (.254) |

- Nenhuma VM usa placa em ponte. A Internet só existe durante as instalações, através de uma placa NAT da VM-FIREWALL (R8), que fica com o cabo desligado durante os testes.

## Consequências

- O filtro fica fora da VM-HONEYPOT, por isso continua ativo mesmo que esta seja comprometida.
- As quatro VMs ocupam 15,5 a 17,5 GB de RAM, o que deixa pelo menos 6,5 GB ao sistema do PC.
- Há uma tarefa nova, a 47 (criar a VM-FIREWALL). As tarefas 4, 5 e 9 passam a referir o VirtualBox, os tipos de rede e as regras na VM-FIREWALL. As tarefas 6, 7 e 39 dependem da 47, porque é por ela que as VMs têm Internet nas instalações.
- Para o fluxo R4, o PC anfitrião precisa de uma rota para 10.10.10.0/24 através de 10.10.20.254.
- O relatório (capítulos 2 e 3, incluindo a Figura 1), o plano de testes e os README foram atualizados.

## Por confirmar no PC

- Sistema operativo. Se for Windows com o Hyper-V ou o WSL2 ativos, o VirtualBox fica mais lento (aparece uma tartaruga verde na janela da VM).
- Virtualização ativa na BIOS (Intel VT-x ou AMD-V).
- Pelo menos 150 GB livres em disco, de preferência num SSD.
- Dias e horários em que o PC está disponível para o grupo.

## Alternativas consideradas

- **Proxmox VE:** filtra o tráfego no próprio hipervisor, mas exige um PC dedicado.
- **Sem quarta VM, com a VM-HONEYPOT ligada também à rede de gestão:** é mais simples, mas o filtro ficaria na máquina atacada. Se esta fosse comprometida, o atacante podia desligá-lo e chegar ao SIEM.
