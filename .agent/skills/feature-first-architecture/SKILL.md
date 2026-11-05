---
name: feature-first-architecture
description: >-
  Arquitetura Vertical Slice / Feature-First com Diálogo Interativo para 5 cenários:
  (1) Nativo Apple Swift/SwiftUI, (2) Full-Stack Web Next.js, (3) Front-End/Mobile
  Cross-Platform, (4) Back-End/APIs e (5) Sistemas de IA/Agentes/RAG.
  REGRA DE ACIONAMENTO (CONFIRMAÇÃO OBRIGATÓRIA): NUNCA acione nem aplique esta
  skill de forma deliberada ou automática. O agente DEVE iniciar pelo diálogo
  interativo (STEP 0) e aguardar confirmação explícita antes de criar ou mover arquivos.
---

# 🏗️ Skill: Feature-First & Vertical Slice Architecture (`feature-first-architecture`)

Arquitetura modular que organiza projetos por **fatias verticais de domínio (`Features/` ou `modulos/`)** em vez de camadas técnicas horizontais, adaptando-se tanto ao **padrão nativo Apple (`Swift / SwiftUI`)** quanto ao **Padrão Universal Vertical Slice** para Web Full-Stack, Front-End/Mobile, Back-End/APIs e Sistemas de IA/Agentes.

---

## 🛑 STEP 0: DIÁLOGO INTERATIVO E CONFIRMAÇÃO OBRIGATÓRIA

> [!IMPORTANT]
> **Esta skill NUNCA deve ser executada de forma autônoma, silenciosa ou deliberada.**
> Antes de criar qualquer pasta, mover arquivos ou refatorar imports, o agente **DEVE** usar a ferramenta `ask_question` (ou perguntar diretamente ao usuário) apresentando as **3 perguntas abaixo** e aguardar a confirmação.

### 📋 Perguntas Obrigatórias do `STEP 0`

Sempre que esta skill for invocada, faça um breve diagnóstico do diretório atual e apresente estas 3 perguntas ao usuário:

1. **Qual é o cenário arquitetural do projeto?**
   - **Cenário 1 — Sim, é um projeto Nativo Apple (`Swift / SwiftUI — iOS / macOS`):** Aplica exatamente a árvore oficial estilo Apple (`App/`, `DesignSystem/`, `Features/<Feature>/`, `Models/`, `Services/`, `Utilities/`, `Preview Content/`).
   - **Cenário 2 — Full-Stack Web / Monólito Modular (`Next.js / Nuxt / Remix / SvelteKit`):** Aplica o Padrão Universal Vertical Slice Full-Stack (Front-end + API + Banco colocalizados por módulo).
   - **Cenário 3 — Front-End Puro ou Mobile Cross-Platform (`React / Vite / React Native / Expo / Flutter`):** Aplica o Padrão Universal Vertical Slice de Cliente (Telas + Subviews + Hooks + API Client + `design-system/` isolado, sem camada de banco).
   - **Cenário 4 — Back-End Puro / APIs & Microsserviços (`Node.js / Fastify / Nest`, `Python / FastAPI`, `Go`, `Rust`):** Aplica o Padrão Universal Vertical Slice de Servidor (`routes`, `usecase`, `model`, `repo`, `test`), sem camada de UI.
   - **Cenário 5 — Sistemas com IA / Agentes / RAG (`Python / TypeScript + LLMs / LangGraph / MCP`):** Aplica o Padrão Universal Vertical Slice para IA (inclui `prompts`, `tools`, `evals` e `services/llm` + banco vetorial).

2. **Qual idioma deseja usar na nomenclatura de pastas e contratos?**
   - **Inglês (`Features/`, `DesignSystem/`, `Services/`, `model.ts`, `index.ts`)** *(Padrão obrigatório no Cenário 1 - Apple)*
   - **Português (`modulos/`, `compartilhado/`, `modelo.ts`, `repo.ts`, `publico.ts`)** *(Padrão `RecicleUai_2026`)*

3. **Como deseja proceder agora?**
   - **Apenas Diagnóstico / Plano (Dry-Run):** Mostrar a árvore proposta e a estimativa de impacto (quantos arquivos/imports seriam afetados) sem alterar nada.
   - **Novo Projeto (Greenfield):** Criar a estrutura base de diretórios e arquivos iniciais agora.
   - **Refatoração Incremental (Strangler Pattern):** Migrar uma feature por vez com verificação estrita (`tsconfig.modulos.json` + testes) sem quebrar o sistema atual.

---

## 🧭 Os 5 Cenários Arquiteturais (Blueprints Oficiais)

### 🍎 Cenário 1: Projeto Nativo Apple (`Swift / SwiftUI — iOS / macOS`)

Quando o usuário responder que **é um projeto Swift / Apple**, aplique **exatamente** esta estrutura:

```text
NomeDoApp/
├── App/                        # @main, configuração raiz, roteamento global e ciclo de vida
├── DesignSystem/               # Tokens, cores, tipografia SF, botões e componentes HIG reutilizáveis
├── Features/                   # Fatias verticais por tela/fluxo do produto
│   ├── Capture/
│   │   ├── CaptureControlsBar.swift      # Subcomponente local da feature
│   │   ├── CaptureCountdownView.swift    # Subcomponente local da feature
│   │   ├── CaptureQualityIndicator.swift # Subcomponente local da feature
│   │   ├── CaptureScreen.swift           # Tela principal da feature
│   │   └── SonarFeedView.swift           # Subcomponente local da feature
│   ├── DiveLog/
│   ├── Settings/
│   ├── SiteList/
│   └── SurveyViewer/
├── Models/                     # APENAS entidades de domínio compartilhadas entre 2+ features
├── Preview Content/            # Mocks, fixtures e assets exclusivos para Xcode Previews
├── Services/                   # Infraestrutura: rede, Bluetooth/Sonar, sensores, CoreData/SwiftData
├── Utilities/                  # Extensions, formatadores, unidades e helpers puros
├── Assets.xcassets             # Ícones, imagens e cores do bundle
└── NomeDoAppTests/             # Testes unitários focados em Models, Utilities e regras de negócio
```

---

### 🌐 Cenário 2: Full-Stack Web / Monólito Modular (`Next.js / TypeScript`)

Quando **não for Swift** e se tratar de uma aplicação Web Full-Stack (onde rotas de página, rotas de API e banco convivem no mesmo repositório):

```text
src/
├── app/                        # Casca fina de rotas (Next.js App Router)
│   ├── (rotas)/page.tsx        # Importa e renderiza a View de src/modulos/<feature>/componentes
│   └── api/<feature>/route.ts  # Repassa a requisição HTTP para src/modulos/<feature>/api.ts
├── components/
│   ├── ui/                     # Design System (BottomSheet, SegmentedControl, EstadoVazio, etc.)
│   └── layout/                 # AppShell, Footer, navegação global
├── modulos/                    # (ou features/) — Fatias verticais Full-Stack completas
│   └── <feature>/
│       ├── modelo.ts           # Regras de negócio PURAS e tipos (zero dependência de DB/HTTP/React)
│       ├── repo.ts             # Queries SQL / ORM exclusivas deste domínio
│       ├── migracoes.ts        # Evolução de schema pertencente ao módulo
│       ├── api.ts              # Caso de uso / handler de servidor da feature
│       ├── cliente.ts          # Hooks e chamadas HTTP para consumo no front-end
│       ├── publico.ts          # Contrato público (única porta de entrada para outros módulos)
│       └── componentes/        # Telas e subcomponentes exclusivos desta feature
└── compartilhado/              # (ou shared/) — Services + Utilities + Models globais
    ├── db/                     # Pool de conexão e runner de migrações
    ├── http/                   # Cliente HTTP, tratamento de erros e sessão
    ├── armazenamento/          # S3, disco local, regras de upload
    └── utilitarios/            # Formatadores, distância geográfica, tema
```

---

### 📱 Cenário 3: Front-End SPA & Mobile Cross-Platform (`React / Vite / React Native / Expo / Flutter`)

Para projetos puramente clientes (Web SPA ou Mobile Híbrido) que consomem APIs externas e não possuem banco de dados direto:

```text
src/
├── app/                        # Providers globais, navegação/rotas raiz e bootstrap
├── design-system/              # Componentes atômicos (Button, Sheet, Input), cores e tipografia
├── features/                   # (ou modulos/)
│   └── capture/
│       ├── CaptureScreen.tsx           # Tela principal da feature
│       ├── CaptureControlsBar.tsx      # Subcomponente local da feature
│       ├── CaptureCountdownView.tsx    # Subcomponente local da feature
│       ├── useCapture.ts               # Hook/estado local da tela
│       └── capture.api.ts              # Chamadas HTTP/SDK exclusivas desta feature
├── models/                     # Interfaces e tipos globais que cruzam 2+ features
├── services/                   # Cliente HTTP base, Storage local, Câmera/GPS, Push Notifications
└── utilities/                  # Funções puras (moeda, data, máscaras, cálculos)
```

---

### ⚙️ Cenário 4: Back-End Puro / APIs & Microsserviços (`Node.js`, `Python / FastAPI`, `Go`, `Rust`)

Para servidores de API, workers e sistemas B2B sem interface gráfica (sem `DesignSystem/`):

```text
src/
├── app/                        # Inicialização do servidor HTTP, middlewares globais, healthcheck
├── features/                   # (ou modulos/) — Cada domínio de negócio em sua própria fatia
│   ├── pedidos/
│   │   ├── pedidos.routes.ts   # (ou router.py) Definição de rotas HTTP do domínio
│   │   ├── pedidos.usecase.ts  # Orquestração da regra de aplicação e transações
│   │   ├── pedidos.model.ts    # Regras de negócio puras + schemas de validação (Zod/Pydantic)
│   │   ├── pedidos.repo.ts     # Acesso ao banco de dados restrito a pedidos
│   │   ├── pedidos.public.ts   # Fachada pública para outros módulos internos
│   │   └── pedidos.test.ts     # Testes unitários e de integração colocalizados
│   ├── pagamentos/
│   └── estoque/
├── models/                     # Eventos de domínio e contratos compartilhados entre features
├── services/                   # Clientes de infraestrutura (Postgres, Redis, RabbitMQ/Kafka, S3)
└── utilities/                  # Criptografia, loggers, parsers e funções puras
```

---

### 🤖 Cenário 5: Sistemas com IA / Agentes Autônomos / RAG (`Python / TypeScript + LLMs`)

Para aplicações centradas em Inteligência Artificial, onde prompts, ferramentas (`tools`), recuperação vetorial (`RAG`) e avaliações (`evals`) fazem parte de cada domínio:

```text
src/
├── app/                        # Entrada da API/CLI/Worker, configuração de telemetria (Langfuse/OTel)
├── features/                   # (ou modulos/) — Cada capacidade ou agente de domínio em uma fatia
│   ├── triagem-documentos/
│   │   ├── model.py            # Schemas estruturados (Pydantic/Zod) e pós-processamento puro
│   │   ├── prompts.py          # System prompts versionados e templates desta feature
│   │   ├── tools.py            # Function calling / ferramentas exclusivas deste agente
│   │   ├── agent.py            # Loop de execução / grafo da feature
│   │   ├── repo.py             # Persistência e busca híbrida (BM25 + pgvector) do domínio
│   │   └── evals.py            # Casos de teste determinísticos e métricas de qualidade da feature
│   └── atendimento-rag/
├── models/                     # Contratos globais de mensagens, sessões, tokens e citações
├── services/
│   ├── llm/                    # Roteador de modelos (Gemini, Claude, OpenAI, vLLM), fallbacks e cache
│   ├── vector-db/              # Cliente pgvector / Qdrant / embeddings
│   └── guardrails/             # Filtros de PII, defesa contra Prompt Injection e rate limit
└── utilities/                  # Chunking de texto, contagem de tokens, parsers Markdown/JSON
```

---

## 📏 As 5 Regras de Ouro (Invariantes em Todos os 5 Cenários)

1. **Coesão por Funcionalidade (Colocation):**
   Arquivos que mudam juntos moram juntos. Tudo o que é exclusivo de uma feature nasce dentro de `Features/<Nome>/` (ou `modulos/<nome>/`).
2. **Regra dos 2 Consumidores:**
   Só promova um componente para `DesignSystem/` ou um tipo para `Models/` quando **pelo menos 2 features distintas** precisarem dele.
3. **Núcleo de Domínio Puro (`model` / `modelo.ts`):**
   Cálculos, validações e máquinas de estado devem ser funções puras sem dependência de banco, rede ou UI.
4. **Fronteira Explícita (`publico.ts` / `public.py`):**
   Em sistemas modulares, uma feature não importa o `repo` privado de outra feature; toda conversa entre domínios passa pelo contrato público.
5. **Subpastas Sob Demanda (Sem Over-engineering):**
   Enquanto uma feature tiver até ~8 arquivos, deixe-os direto na raiz da feature (exatamente como `Features/Capture/` no Cenário 1). Só crie subpastas internas quando ultrapassar 8–10 arquivos.

---

## 🔄 Guia de Refatoração Segura (Strangler Pattern)

Quando o usuário escolher a opção **Refatoração Incremental** no `STEP 0`:

1. **Não mova o projeto inteiro de uma vez.**
2. **Crie checagem estrita para o código novo (`tsconfig.modulos.json`):**
   Ative `strict: true`, `noUnusedLocals: true` e `noImplicitReturns: true` apenas sobre `src/modulos/**/*` (ou `src/features/**/*`) e `src/compartilhado/**/*`.
3. **Migre 1 Feature por vez:**
   Extraia `modelo` + testes unitários ➔ extraia `repo` e `api` ➔ mova componentes ➔ reduza a rota original em `app/` a uma casca fina ➔ rode `typecheck` e testes antes de seguir.
