# Boilerplates

Colecao de estruturas iniciais para projetos, pensadas para serem desenvolvidas com Claude Code, Codex e outras ferramentas de agente de IA.

Cada boilerplate fica em sua propria pasta, e autocontido e traz as instrucoes para os agentes (`AGENTS.md` + `CLAUDE.md`) e um `README.md` com o inicio rapido.

## Catalogo

| Boilerplate | Stack | Descricao |
|-------------|-------|-----------|
| [boilerplate-python-web-agent](boilerplate-python-web-agent/README.md) | Python, FastAPI, Jinja2, LangChain, PostgreSQL, Alembic, pytest | Aplicacao web com agente de IA |

## Como usar

Baixe so a pasta do boilerplate com o [degit](https://github.com/Rich-Harris/degit) (sem o historico do git):

```bash
npx degit fabricioveronez/boilerplates/boilerplate-python-web-agent meu-agente
cd meu-agente
```

Ou clone o repositorio e copie a pasta:

```bash
git clone https://github.com/fabricioveronez/boilerplates.git
cp -r boilerplates/boilerplate-python-web-agent ~/projetos/meu-agente
cd ~/projetos/meu-agente
```

Depois siga o `README.md` do boilerplate. No `boilerplate-python-web-agent`:

```bash
make init name=meu_agente
make dev                    # abra http://localhost:8000
```

## Adicionando um boilerplate

1. Crie uma pasta na raiz no padrao `boilerplate-<linguagem>-<tipo>` (ex.: `boilerplate-node-api`).
2. Mantenha a pasta autocontida — ela deve funcionar depois de copiada sozinha, com seu proprio `.gitignore`.
3. Inclua:
   - `README.md` com inicio rapido, comandos, estrutura e stack
   - `AGENTS.md` com as instrucoes para os agentes e `CLAUDE.md` importando o `AGENTS.md`
   - `.env.example` sem segredos (nunca versione o `.env`)
4. Adicione uma linha na tabela do [Catalogo](#catalogo).
