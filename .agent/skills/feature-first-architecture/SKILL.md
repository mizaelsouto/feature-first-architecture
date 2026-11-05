---
name: feature-first-architecture
description: >-
  Arquitetura Vertical Slice / Feature-First com Design System isolado e Core
  Compartilhado para projetos Web, Full-Stack, Backend/APIs e Mobile (Next.js,
  React, Node.js, Python/FastAPI, Go, React Native, Flutter).
  REGRA DE ACIONAMENTO (CONFIRMAÇÃO OBRIGATÓRIA): NUNCA acione nem aplique esta
  skill de forma deliberada ou automática. O agente DEVE apenas sugerir seu uso ou,
  quando solicitado, apresentar primeiro um plano de diagnóstico e aguardar a
  confirmação explícita do usuário antes de criar ou mover qualquer arquivo.
---

# 🏗️ Skill: Vertical Slice & Feature-First Architecture (`feature-first-architecture`)

Padrão arquitetural universal que organiza qualquer projeto de software por **fatias verticais de negócio (`features/` ou `modulos/`)** em vez de camadas técnicas horizontais (`controllers/`, `views/`, `repositories/` soltos), combinando **alta coesão de domínio**, **Design System desacoplado** e **Core Compartilhado enxuto**.

> **Propósito central:** Trazer a clareza visual de uma árvore enxuta (`app`, `design-system`, `features`, `models`, `services`, `utilities`) para **ambientes Web, Full-Stack, Backend e Mobile Cross-Platform** (Next.js, React, Node.js, Python, Go, React Native, Flutter), tanto na criação de novos projetos quanto na refatoração segura de projetos legados.

---

## 🛑 PROTOCOLO DE SEGURANÇA: PROIBIDO ACIONAMENTO DELIBERADO (STEP 0)

> [!IMPORTANT]
> **Esta skill NUNCA deve ser executada de forma autônoma, silenciosa ou deliberada pelo agente.**

Sempre que você identificar que um projeto novo ou existente poderia se beneficiar desta arquitetura — ou mesmo quando o usuário perguntar sobre organização de pastas:

1. **NÃO crie pastas, NÃO mova arquivos e NÃO altere imports imediatamente.**
2. **Faça primeiro o Diagnóstico de Custo × Benefício:**
   - Identifique a stack atual do projeto (Next.js, React, Node, Python, etc.).
   - Se o projeto já possui fatias verticais bem definidas (ex: `src/modulos/` ou `src/features/`), **recomende NÃO refatorar** por estética, apontando apenas eventuais consolidações pontuais.
   - Se for um projeto novo ou uma refatoração necessária, apresente a **Árvore Proposta adaptada à stack do projeto** e a **Estimativa de Impacto** (quantos arquivos/imports serão afetados).
3. **Peça Confirmação Explícita:**
   - Pergunte claramente ao usuário: *"Deseja que eu aplique a estrutura `feature-first-architecture` agora?"*
   - **Aguarde o "Sim" explícito do usuário** antes de executar qualquer alteração no sistema de arquivos.

---

## 🧭 O Conceito Universal (As 6 Peças do Vertical Slice)

Independentemente da linguagem ou framework, todo projeto bem estruturado neste modelo divide sua raiz de código (`src/`) em responsabilidades óbvias no primeiro olhar:

```text
src/
├── app/                # 1. Entrada, roteamento global, bootstrap e ciclo de vida
├── design-system/      # 2. (Se houver UI) Tokens, tema e componentes visuais puros sem regra de negócio
├── features/           # 3. FATIAS VERTICAIS: cada subpasta é um domínio de negócio completo
│   ├── captura/
│   ├── catalogo/
│   ├── faturamento/
│   └── notificacoes/
├── models/             # 4. Contratos/entidades globais que cruzam 2 ou mais features
├── services/           # 5. Infraestrutura externa (Banco de dados, HTTP, S3, Filas, Auth, Cache)
└── utilities/          # 6. Funções puras, formatadores, cálculos e helpers sem estado
```

*(Em projetos em português, os equivalentes diretos são `app/`, `components/ui/`, `modulos/` e `compartilhado/`).*

---

## 🛠️ Adaptação por Ambiente (Templates Prontos)

### 1. Full-Stack Web / Monólito Modular (Next.js App Router / TypeScript)

No Next.js, `src/app/` funciona apenas como **casca fina de roteamento**, enquanto cada pasta em `src/modulos/` (ou `src/features/`) encapsula o front-end, o back-end e o banco daquela funcionalidade:

```text
src/
├── app/                        # Casca fina de rotas (Next.js App Router)
│   ├── (rotas)/page.tsx        # Apenas importa a View de src/modulos/<feature>/componentes
│   └── api/<feature>/route.ts  # Apenas repassa a request para src/modulos/<feature>/api.ts
├── design-system/              # (ou components/ui/) Botões, modais, inputs, tokens e layout base
├── modulos/                    # FATIAS VERTICAIS COMPLETAS (Full-Stack)
│   └── <feature>/
│       ├── modelo.ts           # Regras de negócio PURAS e tipos locais (zero banco/HTTP/React)
│       ├── repo.ts             # Queries SQL / ORM exclusivas deste domínio
│       ├── migracoes.ts        # Evolução de schema pertencente ao módulo
│       ├── api.ts              # Caso de uso / handler de servidor da feature
│       ├── cliente.ts          # Hooks e cliente HTTP para consumo no front-end
│       ├── publico.ts          # Contrato público (única porta de entrada para outros módulos)
│       └── componentes/        # Telas e subcomponentes exclusivos desta feature
└── compartilhado/              # Unificação de Services + Utilities + Models globais
    ├── db/                     # Conexão com banco e executor de migrações
    ├── http/                   # Cliente HTTP, tratamento de erros e sessão
    ├── armazenamento/          # S3 / disco local
    └── utilitarios/            # Formatadores, datas, geolocalização, validações genéricas
```

---

### 2. Front-End SPA & Mobile Cross-Platform (React / Vite / React Native / Expo / Flutter)

Quando o projeto é exclusivamente cliente (Web SPA ou App Mobile), cada feature agrupa sua tela principal, seus subcomponentes visuais, hooks locais e chamadas de serviço:

```text
src/
├── app/                        # Providers globais, navegação/rotas raiz e configuração do app
├── design-system/              # Componentes atômicos (Button, Sheet, Badge), cores e tipografia
├── features/
│   └── captura/                # Tudo sobre "captura" mora junto (fácil de achar, alterar ou apagar)
│       ├── CapturaScreen.tsx           # Tela principal da feature
│       ├── BarraControlesCaptura.tsx   # Subcomponente usado só aqui
│       ├── IndicadorQualidade.tsx      # Subcomponente usado só aqui
│       ├── useCaptura.ts               # Estado e lógica da tela
│       └── captura.api.ts              # Endpoints consumidos por esta feature
├── models/                     # Tipos e interfaces compartilhados pelo app inteiro (ex: Usuario)
├── services/                   # Cliente HTTP (Axios/Fetch), Storage local, Câmera/GPS, Push
└── utilities/                  # Funções puras (moeda, data, máscaras, matemática)
```

---

### 3. Back-End & APIs (Node.js / Fastify / Express, Python / FastAPI, Go)

Em APIs e microsserviços sem interface gráfica, o `design-system/` sai de cena e as fatias verticais eliminam o antipadrão de pastas gigantes `controllers/`, `services/` e `repositories/`:

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

## 📏 As 5 Regras de Ouro (Para Qualquer Stack)

1. **Coesão por Funcionalidade (Colocation):**
   Arquivos que mudam juntos devem morar juntos. Se um componente, hook, query ou teste só existe por causa da feature `X`, ele fica dentro de `features/X/` — nunca em uma pasta global.
2. **Regra dos 2 Consumidores (Promoção Consciente):**
   Só mova algo de dentro de `features/<nome>/` para `design-system/`, `models/` ou `utilities/` quando **pelo menos 2 features diferentes** precisarem daquele código.
3. **Núcleo de Domínio Puro (`model` / `modelo.ts`):**
   Toda feature deve separar suas regras de negócio puras (cálculos, validações, transições de status) do código de banco de dados e de interface. Funções puras não precisam de mocks para serem testadas.
4. **Fronteira Explícita entre Features (`publico.ts` / `index.ts`):**
   Uma feature **nunca** importa arquivos internos (`repo` ou subcomponentes privados) de outra feature. Se a feature `A` precisa consultar algo da feature `B`, ela importa apenas do contrato público de `B`.
5. **Subpastas Apenas Quando Necessário (Anti-Overengineering):**
   Se uma feature possui de 3 a 8 arquivos, deixe-os direto na pasta da feature (`features/captura/`). Só crie subpastas internas (`componentes/`, `hooks/`, `usecases/`) quando a feature passar de ~8–10 arquivos.

---

## 🔄 Guia de Refatoração Segura (Projetos Existentes / Legados)

Quando o usuário **confirmar explicitamente** que deseja migrar um projeto existente para Vertical Slice, nunca mova tudo de uma vez. Aplique o **Strangler Pattern**:

1. **Congele a bagunça com verificação estrita parcial:**
   Em TypeScript, crie um `tsconfig.modulos.json` (ou regras de lint) com `strict: true` apontando apenas para as pastas novas (`src/features/` ou `src/modulos/` e `src/compartilhado/`), sem quebrar o código legado.
2. **Unifique pastas de infraestrutura duplicadas:**
   Se o projeto tiver `lib/`, `utils/`, `helpers/`, `common/` e `shared/` espalhados, consolide gradualmente em `services/` + `utilities/` (ou `compartilhado/`).
3. **Migre uma Feature de cada vez:**
   - Escolha 1 fluxo isolado;
   - Extraia as regras puras para `modelo` e escreva os testes;
   - Agrupe tela, estado e persistência dentro da fatia vertical;
   - Transforme a rota original em `app/` em uma chamada de 3 linhas para a feature;
   - Valide build + testes antes de tocar na próxima feature.
