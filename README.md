# Boilerplates

Colecao de estruturas iniciais para projetos, pensadas para serem desenvolvidas com Claude Code, Codex, Cursor e outras ferramentas de agente de IA.

Cada boilerplate fica em sua propria pasta, e autocontido e traz tudo que um agente precisa para trabalhar: as instrucoes (`AGENTS.md` + `CLAUDE.md`), um `README.md` com o inicio rapido e a estrutura de codigo e testes ja montada.

## O que e um boilerplate aqui

- **Autocontido** — a pasta funciona sozinha depois de copiada. Ela tem o proprio `.gitignore`, `.env.example` e dependencias.
- **Pronto para agentes** — as convencoes (onde cada camada mora, como rodar e testar) ficam no `AGENTS.md`, entao o agente implementa seguindo o padrao do projeto desde o primeiro prompt.
- **Pronto para rodar** — um comando de inicializacao e outro de desenvolvimento, sem configuracao manual.

## Catalogo

| Boilerplate | Stack | Descricao |
|-------------|-------|-----------|
| [boilerplate-python-web-agent](boilerplate-python-web-agent/README.md) | Python, FastAPI, Jinja2, LangChain, PostgreSQL, Alembic, pytest | Aplicacao web com agente de IA |

## Como usar

Escolha o boilerplate desejado e baixe **apenas a pasta dele**, sem o historico do git, com o [degit](https://github.com/Rich-Harris/degit):

```bash
npx degit crilsen/ai-boilerplates-agent/boilerplate-python-web-agent meu-agente
cd meu-agente
```

Sem Node instalado, clone o repositorio e copie a pasta:

```bash
git clone https://github.com/crilsen/ai-boilerplates-agent.git
cp -r ai-boilerplates-agent/boilerplate-python-web-agent ~/projetos/meu-agente
cd ~/projetos/meu-agente
```

Depois siga o `README.md` do boilerplate. No `boilerplate-python-web-agent`, o inicio rapido e:

```bash
make init name=meu_agente   # renomeia o package, cria o .env e instala as dependencias
make dev                    # abra http://localhost:8000
```

## Adicionando um boilerplate

1. Crie uma pasta na raiz no padrao `boilerplate-<linguagem>-<tipo>` (ex.: `boilerplate-node-api`).
2. Mantenha a pasta autocontida — ela deve funcionar depois de copiada sozinha, com seu proprio `.gitignore`.
3. Inclua:
   - `README.md` com inicio rapido, comandos, estrutura e stack;
   - `AGENTS.md` com as instrucoes para os agentes e `CLAUDE.md` importando o `AGENTS.md`;
   - `.env.example` sem segredos (nunca versione o `.env`).
4. Adicione uma linha na tabela do [Catalogo](#catalogo).

## Creditos

Este repositorio e um fork de [fabricioveronez/boilerplates](https://github.com/fabricioveronez/boilerplates). Os boilerplates originais sao de autoria de [@fabricioveronez](https://github.com/fabricioveronez); este fork mantem os creditos e evolui a estrutura.
