# Como trabalhar neste repositório

## Ligação ao Jira

Cada tarefa do Jira tem uma chave no formato `HPT-<número>` (por exemplo, `HPT-10`).
Usem a chave sempre em branches, commits e pull requests: com a integração do GitHub no Jira, o código aparece ligado à tarefa.

## Branches

- `main` é a versão estável: cada tarefa é feita na sua branch e integrada por pull request.
- Cada tarefa tem a sua branch, no formato `tipo/CHAVE-descricao-curta`:
  - `feat/HPT-12-cowrie-compose`
  - `fix/HPT-18-decoder-cowrie`
  - `docs/HPT-30-seccao-infra`

## Commits

Formato: `tipo(área): descrição [CHAVE]`

```
feat(sensors): adiciona docker-compose do Cowrie [HPT-12]
fix(siem): corrige o decoder de cowrie.login.failed [HPT-18]
docs(infra): documenta as redes virtuais [HPT-5]
```

- Tipos: `feat`, `fix`, `docs`, `test`, `chore`.
- Áreas: `infra`, `sensors`, `siem`, `datasets`, `scripts`, `tests`, `analise`, `docs`.

## Pull requests

1. Abrir o PR para `main` com a chave do Jira no título.
2. A revisão por outro elemento do grupo é recomendada, sobretudo em configurações de segurança, mas não é obrigatória.
3. Fazer merge com squash e apagar a branch.
4. Mover a tarefa para "Concluído" no Jira.

## Antes de cada push

- [ ] Sem palavras-passe, chaves ou `.env`
- [ ] Sem malware nem ficheiros capturados pelos iscos
- [ ] Sem datasets em bruto nem imagens de VMs
- [ ] Configurações testadas no laboratório (ou indicado no PR que não foram)
