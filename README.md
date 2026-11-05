# 🏗️ Feature-First & Vertical Slice Architecture (`feature-first-architecture`)

> **Skill Universal de Arquitetura em Fatias Verticais (Vertical Slice) com Design System Isolado e Core Compartilhado para Web, Full-Stack, Back-End/APIs e Mobile**  
> **Autor:** Mizael Souto  
> **Modo de Operação:** 🔒 **Somente com Confirmação Explícita** *(Nunca executa de forma autônoma ou deliberada)*

---

## 📌 Visão Geral

A maioria dos projetos começa organizada por **camadas técnicas horizontais** (`controllers/`, `services/`, `repositories/`, `views/`, `hooks/`). Conforme o sistema cresce, alterar uma única funcionalidade exige caçar arquivos em 6 pastas diferentes.

A skill **`feature-first-architecture`** resolve isso aplicando o modelo **Vertical Slice (Feature-First)** em **qualquer stack moderna** (Next.js, React, Node.js, Python/FastAPI, Go, React Native, Flutter), traduzindo o conceito visual de 6 zonas claras para a realidade de cada ambiente:

```text
src/
├── app/                # 1. Entrada, roteamento global, bootstrap e ciclo de vida
├── design-system/      # 2. (Quando há UI) Tokens, tema e componentes visuais puros
├── features/           # 3. FATIAS VERTICAIS: cada subpasta é um domínio de negócio completo
│   ├── captura/
│   ├── catalogo/
│   ├── faturamento/
│   └── notificacoes/
├── models/             # 4. Contratos e entidades globais que cruzam 2+ features
├── services/           # 5. Infraestrutura externa (Banco, HTTP, S3, Filas, Auth, Cache)
└── utilities/          # 6. Funções puras, formatadores, cálculos e helpers sem estado
```

---

## 🔒 Trava de Segurança: Proibido Acionamento Deliberado

Esta skill possui **dupla proteção** (no `description` do cabeçalho YAML e no `STEP 0` do corpo de instruções) para impedir que o agente saia movendo pastas ou refatorando código por conta própria:

1. **Diagnóstico Prévio de Custo × Benefício:** Antes de tocar em qualquer arquivo, o agente identifica a stack e avalia se a mudança traz ganho real. Se o projeto já estiver bem estruturado, o agente recomendará **não mexer**.
2. **Plano de Impacto:** Caso a criação ou refatoração faça sentido, o agente apresenta a árvore proposta adaptada à stack do projeto e quantos arquivos/imports serão afetados.
3. **Confirmação Explícita:** Nenhuma pasta é criada ou movida sem o **"Sim" explícito** do desenvolvedor.

---

## 🛠️ Como o Conceito se Adapta a Cada Ambiente

| Zona Conceitual | Full-Stack (Next.js / TS) | Front-End / Mobile (React, RN, Flutter) | Back-End / APIs (Node, Python, Go) |
| :--- | :--- | :--- | :--- |
| **1. `app/`** | `src/app/` (Rotas finas `page.tsx` e `api/`) | `src/app/` (Providers, Router, Bootstrap) | `src/app/` (Servidor HTTP, Middlewares) |
| **2. `design-system/`** | `src/design-system/` ou `src/components/ui/` | `src/design-system/` (Botões, Inputs, Tema) | *(Não aplicável em APIs puras)* |
| **3. `features/`** | `src/modulos/<feature>/` (Full-Stack Slice) | `src/features/<feature>/` (Tela + Subviews + Hooks) | `src/features/<feature>/` (Route + UseCase + Model + Repo) |
| **4. `models/`** | `modelo.ts` local + tipos globais | `src/models/` (Entidades compartilhadas) | `src/models/` (Eventos e contratos globais) |
| **5. `services/`** | `src/compartilhado/{db,http,armazenamento}` | `src/services/` (API Client, Storage, GPS) | `src/services/` (Postgres, Redis, Filas, S3) |
| **6. `utilities/`** | `src/compartilhado/utilitarios/` | `src/utilities/` (Formatadores, Datas) | `src/utilities/` (Crypto, Parsers, Loggers) |

---

### 🌐 1. Full-Stack Web / Monólito Modular (Next.js App Router)

```text
src/
├── app/                        # Casca fina de rotas (Next.js App Router)
│   ├── (rotas)/page.tsx        # Apenas importa a View de src/modulos/<feature>/componentes
│   └── api/<feature>/route.ts  # Apenas repassa a request para src/modulos/<feature>/api.ts
├── design-system/              # (ou components/ui/) Componentes visuais reutilizáveis e tokens
├── modulos/                    # FATIAS VERTICAIS COMPLETAS (Full-Stack)
│   └── <feature>/
│       ├── modelo.ts           # Regras de negócio PURAS e tipos locais (zero banco/HTTP/React)
│       ├── repo.ts             # Queries SQL / ORM exclusivas deste domínio
│       ├── migracoes.ts        # Evolução de schema pertencente ao módulo
│       ├── api.ts              # Caso de uso / handler de servidor da feature
│       ├── cliente.ts          # Hooks e cliente HTTP para consumo no front-end
│       ├── publico.ts          # Contrato público (única porta de entrada para outros módulos)
│       └── componentes/        # Telas e subcomponentes exclusivos desta feature
└── compartilhado/              # Services + Utilities + Models globais unificados
    ├── db/                     # Conexão com banco e executor de migrações
    ├── http/                   # Cliente HTTP, tratamento de erros e sessão
    ├── armazenamento/          # S3 / disco local
    └── utilitarios/            # Formatadores, datas, geolocalização, validações genéricas
```

---

### 📱 2. Front-End SPA & Mobile (React / Vite / React Native / Expo / Flutter)

```text
src/
├── app/                        # Providers globais, navegação/rotas raiz e configuração do app
├── design-system/              # Componentes atômicos (Button, Sheet, Badge), cores e tipografia
├── features/
│   └── captura/                # Tudo sobre "captura" mora junto (alta coesão)
│       ├── CapturaScreen.tsx           # Tela principal da feature
│       ├── BarraControlesCaptura.tsx   # Subcomponente local
│       ├──ContagemRegressivaView.tsx   # Subcomponente local
│       ├── IndicadorQualidade.tsx      # Subcomponente local
│       ├── useCaptura.ts               # Estado e lógica da tela
│       └── captura.api.ts              # Endpoints consumidos por esta feature
├── models/                     # Tipos e interfaces compartilhados pelo app inteiro
├── services/                   # Cliente HTTP, Storage local, Câmera/Sensores, Push
└── utilities/                  # Funções puras (moeda, data, máscaras, matemática)
```

---

### ⚙️ 3. Back-End & APIs (Node.js / Fastify / Nest, Python / FastAPI, Go)

```text
src/
├── app/                        # Inicialização do servidor HTTP, middlewares globais, healthcheck
├── features/                   # Cada domínio de negócio em sua própria pasta vertical
│   ├── pedidos/
│   │   ├── pedidos.routes.ts   # Definição de rotas HTTP do domínio
│   │   ├── pedidos.usecase.ts  # Orquestração da regra de aplicação
│   │   ├── pedidos.model.ts    # Regras de negócio puras + schemas de validação (Zod/Pydantic)
│   │   ├── pedidos.repo.ts     # Acesso ao banco de dados restrito a pedidos
│   │   └── pedidos.test.ts     # Testes unitários e de integração colocalizados
│   ├── pagamentos/
│   └── estoque/
├── models/                     # Eventos de domínio e contratos compartilhados entre features
├── services/                   # Clientes de infraestrutura (Postgres, Redis, RabbitMQ/Kafka, S3)
└── utilities/                  # Criptografia, loggers, parsers e funções puras
```

---

## 📏 As 5 Regras de Ouro

1. **Coesão por Funcionalidade (Colocation):** Arquivos que mudam juntos moram juntos dentro de `features/<nome>/`.
2. **Regra dos 2 Consumidores:** Só promova um componente para `design-system/` ou um tipo para `models/` quando **pelo menos 2 features distintas** precisarem dele.
3. **Núcleo de Domínio Puro (`model` / `modelo.ts`):** Regras de validação, cálculos e máquinas de estado devem ser funções puras (sem banco, sem `fetch`, sem UI), permitindo testes unitários instantâneos.
4. **Fronteira Explícita (`publico.ts` / `index.ts`):** Uma feature nunca importa arquivos internos (`repo` ou subcomponentes privados) de outra feature; toda comunicação entre domínios passa pelo contrato público.
5. **Subpastas Sob Demanda:** Se uma feature tiver até ~8 arquivos, mantenha-os direto na raiz da feature. Só crie subpastas internas quando passar de 8–10 arquivos.

---

## 🚀 Instalação e Uso

### No Google Antigravity (`skills.json`)
```json
{
  "path": "/Users/upper/Documents/Projetos /1_I-A_Skills/feature-first-architecture/.agent/skills"
}
```

### Como Acionar (Sempre com Confirmação)
- *"Faça um diagnóstico deste projeto usando a skill `feature-first-architecture`"*
- *"Estruture este novo projeto seguindo a skill `feature-first-architecture`"*

---

## 📚 Documentação Completa

- [`SKILL.md`](./SKILL.md) — Instruções operacionais da skill para o agente.
- [`docs/GUIA_DETALHADO.md`](./docs/GUIA_DETALHADO.md) — Contratos internos de cada camada, exemplos multi-stack e checklist de migração incremental (Strangler Pattern).
