# Sensores

Responsável: Guilherme Barros.

- `cowrie/` – `docker-compose.yml` e `cowrie.cfg` (SSH 2222, Telnet 2223). Tarefas 12 e 36.
- `dionaea/` – `docker-compose.yml` e serviços, com o `log_json` ativo. Tarefas 13 e 36. Se a máquina anfitriã for um Mac (ARM), usar a imagem `cowrie/dionaea`.

O isolamento dos containers (tarefa 14) é configurado nos próprios `docker-compose.yml`.
Os logs e os ficheiros capturados ficam em volumes fora do Git.
