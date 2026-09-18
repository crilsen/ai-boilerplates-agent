# Boilerplate: Aplicacao Web + Agente de IA em Python

Estrutura inicial para projetos web com agentes de IA, pensada para ser desenvolvida com Claude Code, Codex, Cursor e outras ferramentas de agente.

Voce comeca com uma aplicacao FastAPI que ja sobe (pagina web, API, testes e migrations) e uma arquitetura em camadas pronta para receber a logica do seu produto e os seus agentes. Em vez de montar tudo do zero, o agente de IA ja encontra convencoes claras para seguir.

## Stack

| Tecnologia | Uso |
|------------|-----|
| FastAPI + Uvicorn | Backend async |
| Jinja2 | Interface web server-side (dark theme) |
| LangChain | Framework de agentes de IA |
| SQLAlchemy 2.0 + asyncpg | ORM async com PostgreSQL |
| Alembic | Migrations |
| pytest | Testes |
| Docker Compose | Banco de dados local (PostgreSQL + pgAdmin) |
| uv + hatchling | Gerenciador de pacotes + build |

Alem do codigo, o boilerplate inclui `AGENTS.md` (instrucoes para os agentes de IA), `CLAUDE.md` (importa o `AGENTS.md` para o Claude Code) e `docs/` com espacos para PRDs e ADRs.

## Pre-requisitos

Voce precisa de `uv` e `make`. O Docker so e necessario quando for usar o banco de dados.

| Ferramenta | Para que serve | Como instalar |
|------------|----------------|---------------|
| [uv](https://docs.astral.sh/uv/getting-started/installation/) | Gerenciar Python e dependencias | `curl -LsSf https://astral.sh/uv/install.sh \| sh` (macOS/Linux) ou [outros metodos](https://docs.astral.sh/uv/getting-started/installation/) |
| make | Rodar os comandos do projeto | Ja vem no macOS/Linux; no Windows use WSL |
| [Docker](https://docs.docker.com/get-docker/) | Subir o PostgreSQL local (opcional) | Docker Desktop |

Para conferir se esta tudo instalado:

```bash
uv --version
make --version
docker --version   # opcional, so para o banco
```

Nao e necessario ter Python instalado: o `uv` baixa e gerencia a versao correta (3.12+) automaticamente.

## Inicio rapido

Passo a passo do zero ate ver a aplicacao rodando.

### 1. Obtenha o boilerplate

Com Node instalado, use o `degit` para baixar so esta pasta (sem o historico do git):

```bash
npx degit fabricioveronez/boilerplates/boilerplate-python-web-agent meu-agente
cd meu-agente
```

Sem Node, clone o repositorio e copie a pasta:

```bash
git clone https://github.com/fabricioveronez/boilerplates.git
cp -r boilerplates/boilerplate-python-web-agent ~/projetos/meu-agente
cd ~/projetos/meu-agente
```

### 2. Inicialize o projeto

```bash
make init name=meu_agente
```

O `make init` faz tres coisas:

1. Renomeia o package de `my_agent_app` para `name` (aqui, `meu_agente`) em todo o codigo. O parametro e opcional: sem ele, o package continua `my_agent_app`.
2. Cria o `.env` a partir do `.env.example` (se ainda nao existir).
3. Instala as dependencias com `uv sync`.

O `name` deve ser `snake_case` (letras minusculas, numeros e `_`), ex.: `meu_agente` ou `chat_financeiro`.

### 3. Suba a aplicacao

```bash
make dev
```

Abra `http://localhost:8000` e voce vera a pagina **Hello World**.

O `make dev` roda a aplicacao com reload: cada alteracao no codigo reinicia o servidor automaticamente. A aplicacao sobe **sem PostgreSQL e sem `ANTHROPIC_API_KEY`** — banco e LLM so sao necessarios quando o codigo passar a usa-los.

### 4. Endpoints disponiveis de saida

| URL | O que e |
|-----|---------|
| `http://localhost:8000/` | Pagina HTML (Hello World) |
| `http://localhost:8000/api/hello` | `{"message": "Hello World"}` |
| `http://localhost:8000/api/health` | Health check: `{"status": "ok"}` |
| `http://localhost:8000/docs` | Documentacao interativa da API (Swagger UI) |

## Estrutura do projeto

```
.
├── AGENTS.md              # Instrucoes para agentes de IA (fonte unica)
├── CLAUDE.md              # Importa o AGENTS.md para o Claude Code
├── Makefile               # Inicializacao e comandos do dia a dia
├── docker-compose.yml     # PostgreSQL + pgAdmin
├── alembic.ini
├── migrations/            # Alembic (async). Le DATABASE_URL e Base.metadata
├── docs/
│   ├── prds/              # Requisitos de produto (um arquivo por feature)
│   └── adrs/              # Decisoes de arquitetura
├── src/my_agent_app/
│   ├── main.py            # FastAPI + lifespan + registro de routers
│   ├── config.py          # Leitura das variaveis de ambiente
│   ├── database.py        # Base SQLAlchemy + dependency get_session
│   ├── api/router.py      # Rotas JSON sob /api (/api/health, /api/hello)
│   ├── web/router.py      # Pagina inicial + paginas de erro (Jinja2)
│   ├── templates/         # base.html, home.html, error.html
│   ├── services/          # (vazio) Regras de negocio e orquestracao
│   ├── models/            # (vazio) Modelos SQLAlchemy
│   └── agents/            # (vazio) Agentes LangChain
└── tests/                 # pytest
```

Cada pasta tem um papel fixo:

- `api/` e `web/` — a porta de entrada. `api/` responde JSON, `web/` responde HTML.
- `services/` — onde vive a regra de negocio e a orquestracao.
- `models/` — tabelas do banco (SQLAlchemy).
- `agents/` — agentes e tools de IA (LangChain).

## Arquitetura em camadas

O fluxo entre as camadas e sempre no mesmo sentido:

```
web/ e api/  ->  services/  ->  agents/ e models/
```

As regras que mantem o projeto organizado:

- **Routers sao finos.** Validam a entrada, chamam um service e devolvem a resposta. Nao colocam regra de negocio nem chamam LLM direto na rota.
- **`services/` orquestra.** Usa modelos para persistir e agentes para raciocinar. E o lugar da logica.
- **`agents/` e independente.** Nao conhece FastAPI nem `Request`; recebe dados e devolve resultados. Isso o torna testavel isoladamente.
- **Banco** — as sessoes vem de `Depends(get_session)`; o engine e criado no `lifespan` de `main.py`. Nunca crie engine fora dali.
- **Async** em tudo que faz I/O (rotas, banco, LLM, HTTP).

Essa separacao existe para que o agente de IA (e voce) saibam exatamente onde cada coisa mora, evitando que a logica se espalhe pelas rotas.

## Como fazer as tarefas comuns

### Adicionar um endpoint de API

1. Crie o service em `services/` com a logica.
2. Exponha a rota em `api/router.py` (ou em um novo router importado em `main.py`). A rota so valida a entrada e chama o service.
3. Escreva um teste em `tests/test_<assunto>.py` usando a fixture `client`.
4. Rode `make test`.

### Adicionar uma pagina web

1. Crie o template em `templates/` estendendo `base.html`.
2. Registre a rota em `web/router.py` retornando `templates.TemplateResponse(...)`.
3. Estilos genericos (`card`, `btn`, `empty-state`) ja estao no `base.html`; estilos especificos vao no bloco `extra_style` do template.

### Adicionar persistencia (model + migration)

1. Crie o modelo em `models/<nome>.py` herdando de `Base`.
2. Importe o modelo em `models/__init__.py` — sem isso o Alembic nao o enxerga.
3. Gere a migration e aplique:

   ```bash
   make revision m="cria tabela x"
   make migrate
   ```

4. Revise o arquivo gerado em `migrations/versions/` antes de aplicar: o autogenerate nem sempre acerta tudo.

### Criar um agente

O diretorio `agents/` comeca vazio. A convencao esta detalhada no `AGENTS.md`, em resumo:

- `agents/llm.py` — **unico ponto** que instancia o modelo, com `get_chat_model()` lendo `config.get_llm_model()` (formato `provider:modelo`).
- `agents/<nome>_agent.py` — um agente por arquivo, com `create_agent` e uma funcao async de alto nivel (`async def run_<nome>(...)` usando `ainvoke`).
- `agents/tools/<dominio>.py` — tools com `@tool`; type hints e docstring viram o schema. Tools que tocam I/O devem ser async.
- `agents/prompts/<nome>.md` — system prompts longos.

Nenhum teste unitario chama a API real do LLM: substitua o modelo por um fake/mock nos testes.

## Comandos

Rode `make` (sem alvo) para ver todos os comandos disponiveis.

| Comando | O que faz |
|---------|-----------|
| `make init [name=meu_agente]` | Inicializa o projeto (renomeia o package, cria o `.env` e instala as dependencias) |
| `make rename name=meu_agente` | Renomeia o package depois do `init` |
| `make dev` | Sobe o app com reload em `http://localhost:8000` |
| `make test` | Roda os testes (`uv run pytest`) |
| `make db-up` / `make db-down` | Sobe/derruba PostgreSQL + pgAdmin via Docker Compose |
| `make migrate` | Aplica as migrations (`alembic upgrade head`) |
| `make revision m="..."` | Gera migration por autogenerate a partir dos modelos |
| `make setup-full` | `db-up` + `migrate` (sobe o banco e aplica as migrations) |

Dependencias sao gerenciadas com `uv`: adicione com `uv add <pacote>` (ou `uv add --dev <pacote>`), **nunca** com `pip install`.

## Variaveis de ambiente

Ficam no `.env` (criado a partir do `.env.example`). O `.env` nunca e versionado.

| Variavel | Descricao | Padrao |
|----------|-----------|--------|
| `DATABASE_URL` | Connection string PostgreSQL async | `postgresql+asyncpg://app:app123@localhost:5432/my_agent_app` |
| `ANTHROPIC_API_KEY` | Chave de API da Anthropic | (obrigatoria so ao usar agentes) |
| `LLM_MODEL` | Modelo dos agentes, formato `provider:modelo` | `anthropic:claude-sonnet-5` |

Ao adicionar uma variavel nova, leia-a em `config.py`, coloque-a no `.env.example` e na tabela do `AGENTS.md`.

## Banco de dados

O Docker provisiona **apenas o banco**. A aplicacao roda localmente via `uv`.

| Servico | Porta | Credenciais |
|---------|-------|-------------|
| PostgreSQL 17 | 5432 | `app` / `app123` / `my_agent_app` |
| pgAdmin | 5050 | `admin@admin.com` / `admin123` |

Fluxo tipico:

```bash
make db-up         # sobe PostgreSQL + pgAdmin
make migrate       # aplica as migrations
# ... desenvolve, cria/edita models ...
make revision m="cria tabela x"
make migrate
make db-down       # derruba os containers quando terminar
```

Ou, para subir o banco ja com as migrations aplicadas: `make setup-full`.

## Testes

Os testes ficam em `tests/test_<assunto>.py` e usam a fixture `client` (definida em `tests/conftest.py`), que sobe a aplicacao com `TestClient`.

```bash
make test
```

Todo endpoint novo ganha teste. Rode `make test` antes de concluir qualquer tarefa.

## Trabalhando com agentes de IA

O projeto foi feito para ser desenvolvido em parceria com agentes de IA:

- **`AGENTS.md`** e a fonte unica de instrucoes (comandos, arquitetura, convencoes, como criar agentes). Leia-o antes de implementar.
- **`CLAUDE.md`** apenas importa o `AGENTS.md`, para o Claude Code. Ao mudar comandos, arquitetura ou convencoes, atualize o `AGENTS.md`, nao o `CLAUDE.md`.
- **`docs/prds/`** — escreva os requisitos de produto (um arquivo por feature, `NNN-nome.md`) **antes** de pedir a implementacao.
- **`docs/adrs/`** — registre decisoes de arquitetura (`NNN-titulo.md`).
- **`docs/trd.md`** — requisitos tecnicos e decisoes globais (crie quando necessario).

Comece descrevendo o proposito da sua aplicacao na secao "Sobre o projeto" do `AGENTS.md`.

## Solucao de problemas

| Sintoma | Causa provavel | Solucao |
|---------|----------------|---------|
| `Erro: uv nao encontrado` | `uv` nao instalado ou fora do PATH | Instale pelo [link oficial](https://docs.astral.sh/uv/getting-started/installation/) |
| `Aviso: docker nao encontrado` | Docker ausente (so afeta o banco) | Instale o Docker Desktop ou ignore, se nao for usar banco |
| Porta 8000 em uso | Outra aplicacao na mesma porta | Rode `make dev PORT=8001` |
| `make migrate` falha na conexao | PostgreSQL nao esta rodando | Rode `make db-up` antes |
| Migration nao detecta o modelo novo | Modelo nao importado em `models/__init__.py` | Importe o modelo e rode `make revision` de novo |
| Erro de API key ao usar agente | `ANTHROPIC_API_KEY` vazia no `.env` | Preencha a chave (e `LLM_MODEL`, se quiser trocar o modelo) |

## Proximos passos

1. **Descreva o projeto** na secao "Sobre o projeto" do `AGENTS.md`.
2. **Configure o LLM** — preencha `ANTHROPIC_API_KEY` (e, se quiser, `LLM_MODEL`) no `.env` e siga a secao de agentes do `AGENTS.md`.
3. **Suba o banco** quando precisar de persistencia: `make setup-full`.
4. **Escreva os requisitos** em `docs/prds/` e as decisoes tecnicas em `docs/adrs/` antes de pedir a implementacao ao agente.
