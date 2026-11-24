# 0001 – Trabalhar só em laboratório local

- **Data:** 2026-11-23
- **Estado:** aceite pelo grupo e validada pelo professor (tarefa 3)

## Contexto

O honeypot só recebe ataques reais se estiver acessível a partir da Internet. Isso exigia a autorização e a configuração da TI do ISTEC, ou um servidor na cloud. O grupo não tem orçamento e considera pouco provável obter acesso à rede do ISTEC.

## Decisão

Todo o sistema corre numa máquina anfitriã do grupo, em redes virtuais internas, sem qualquer exposição à Internet:

- uma máquina Kali, numa rede atacante, simula a Internet e lança ataques controlados;
- os ataques reais vêm de datasets públicos de honeypots, convertidos e importados no Wazuh.

## Consequências

- Custo zero, nenhuma dependência de terceiros e nenhum risco para redes reais.
- Os dados reais não são recolhidos pelo grupo, por isso os IoCs têm menos valor prático.
- Os objetivos do relatório (secção 1.2) e a arquitetura (capítulo 3) têm de ser atualizados (tarefa 46).
- Deixam de existir as tarefas ligadas à exposição: 10, 28, 37, 38, 40 e 41.

## Alternativas consideradas

- **Exposição através da rede do ISTEC:** dados reais e originais, mas dependente da aprovação da TI e com risco para a reputação da escola.
- **Servidor na cloud:** possível sem pagar, com créditos de estudante ou planos gratuitos, mas exige cartão ou elegibilidade e foi posto de parte pelo grupo.
