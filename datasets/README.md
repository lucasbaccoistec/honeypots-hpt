# Datasets públicos

Responsável: Guilherme Barros (tarefas 44 e 45).

Os datasets em bruto não vão para o Git: cada elemento descarrega-os para `raw/`.

| Dataset | Conteúdo | Onde está descrito |
|---|---|---|
| CyberLab honeynet dataset | Eventos do Cowrie recolhidos em cerca de 50 nós (IPs pseudonimizados) | [Zenodo](https://zenodo.org/records/3687527) |
| MURHCAD | 132 425 eventos do Cowrie, do Dionaea e do SentryPeer, recolhidos em junho de 2025 | [arXiv](https://arxiv.org/html/2601.05813v1) |

A licença, o formato e o local exato de download de cada um são confirmados na tarefa 44.

- `raw/` – ficheiros originais (ignorados pelo Git).
- `convert/` – script que converte os eventos para o formato JSON do Cowrie e do Dionaea e os entrega ao agente Wazuh (tarefa 45).
