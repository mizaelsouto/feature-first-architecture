# 🏗️ Feature-First Architecture (`feature-first-architecture`)

> **Skill de Arquitetura em Fatias Verticais (Feature-First / Vertical Slice) com Design System Isolado e Core Compartilhado**  
> **Autor:** Mizael Souto  
> **Modo de Operação:** 🔒 **Somente com Confirmação Explícita** *(Nunca executa de forma autônoma ou deliberada)*

---

## 📌 Visão Geral

A skill **`feature-first-architecture`** estabelece um padrão arquitetural pragmático para **novos projetos** e **refatoração segura de projetos existentes**, organizando o código por **funcionalidade/domínio (Features / Módulos)** em vez de camadas técnicas horizontais (`controllers/`, `views/`, `services/` globais).

Ela combina o melhor de dois mundos validados em produção:
1. **Padrão Nativo Apple (Swift / SwiftUI):** Separação limpa entre `App/`, `DesignSystem/`, `Features/`, `Models/`, `Services/` e `Utilities/`.
2. **Padrão Monólito Modular Full-Stack (Next.js App Router / TypeScript):** Isolamento completo por domínio em `src/modulos/<feature>/` com regras puras (`modelo.ts`), persistência local (`repo.ts` + `migracoes.ts`), contrato público (`publico.ts`) e tipagem estrita incremental (`tsconfig.modulos.json`).

---

## 🔒 Trava de Segurança: Proibido Acionamento Deliberado

Esta skill possui **dupla proteção** (no `description` do cabeçalho YAML e no `STEP 0` do corpo de instruções) para garantir que nenhum agente de IA saia movendo pastas ou refatorando código por conta própria:

1. **Diagnóstico Prévio Obrigatório:** Antes de tocar em qualquer arquivo, o agente avalia a relação **Custo × Benefício**. Se o projeto já estiver bem estruturado, o agente recomendará **não mexer**.
2. **Plano de Impacto:** Caso a criação ou refatoração faça sentido, o agente apresenta a árvore proposta e quantos arquivos/imports serão afetados.
3. **Confirmação Explícita:** Nenhuma pasta é criada ou movida sem o **"Sim" explícito** do desenvolvedor.

---

## 🧭 Anatomia da Arquitetura (As 4 Zonas)

| Zona | Apple / SwiftUI | Next.js / Full-Stack TS | Responsabilidade |
| :--- | :--- | :--- | :--- |
| **1. Entrada & Rotas** | `App/` | `src/app/` | Ponto de entrada (`@main` / `layout.tsx`), ciclo de vida e casca fina de rotas. |
| **2. Design System** | `DesignSystem/` | `src/components/ui/` | Componentes visuais puros, tokens, cores e tipografia (zero regra de negócio). |
| **3. Funcionalidades** | `Features/<Feature>/` | `src/modulos/<feature>/` | Domínios autossuficientes contendo telas, subcomponentes, estado e regras da feature. |
| **4. Core Compartilhado** | `Models/`, `Services/`, `Utilities/` | `src/compartilhado/` | Infraestrutura transversal (banco, HTTP, S3, geolocalização) e entidades globais. |

---

### 📱 Estrutura 1: Nativo Apple (iOS / macOS — Swift & SwiftUI)

```text
NomeDoApp/
├── App/                        # @main, configuração raiz, roteamento global e ciclo de vida
├── DesignSystem/               # Tokens, cores, tipografia SF, botões e componentes HIG reutilizáveis
├── Features/                   # Fatias verticais por tela/fluxo do produto
│   ├── Capture/
│   │   ├── CaptureScreen.swift           # Tela principal da feature
│   │   ├── CaptureControlsBar.swift      # Subcomponente local da feature
│   │   ├── CaptureCountdownView.swift    # Subcomponente local da feature
│   │   ├── CaptureQualityIndicator.swift # Subcomponente local da feature
│   │   └── SonarFeedView.swift           # Subcomponente local da feature
│   ├── DiveLog/
│   ├── Settings/
│   ├── SiteList/
│   └── SurveyViewer/
├── Models/                     # APENAS entidades de domínio que cruzam 2+ features
├── Services/                   # Infraestrutura: API, Bluetooth, sensores, persistência
├── Utilities/                  # Extensions, formatadores e funções puras sem estado
├── Preview Content/            # Mocks e assets exclusivos para Xcode Previews
└── Assets.xcassets             # Ícones, imagens e cores do bundle
```

---

### 🌐 Estrutura 2: Web & Full-Stack (Next.js App Router / TypeScript)

```text
src/
├── app/                        # Casca fina do Next.js (roteamento páginas + rotas HTTP)
│   ├── (rotas)/page.tsx        # Importa e renderiza a tela de src/modulos/<feature>/componentes
│   └── api/<feature>/route.ts  # Repassa a requisição para src/modulos/<feature>/api.ts
├── components/
│   ├── ui/                     # Design System (BottomSheet, SegmentedControl, EstadoVazio, etc.)
│   └── layout/                 # AppShell, Footer, navegação global
├── modulos/                    # Cada pasta é um domínio Full-Stack isolado
│   └── <feature>/
│       ├── modelo.ts           # Regras de negócio PURAS e tipos (zero dependência de DB/HTTP/React)
│       ├── repo.ts             # Consultas SQL / persistência exclusivas do módulo
│       ├── migracoes.ts        # Evolução de schema pertencente ao módulo
│       ├── api.ts              # Lógica de servidor / controllers da feature
│       ├── cliente.ts          # Hooks e chamadas HTTP do lado do cliente
│       ├── publico.ts          # Fachada pública (único arquivo que outros módulos podem importar)
│       └── componentes/        # Componentes React específicos desta feature
└── compartilhado/              # Infraestrutura transversal (Services + Utilities unificados)
    ├── db/                     # Pool de conexão e runner de migrações
    ├── http/                   # Cliente fetch, padronização de erros, respostas e sessão
    ├── armazenamento/          # S3, disco local, regras de upload
    ├── localizacao/            # GPS, cálculo de distância, geocodificação
    └── tema.ts                 # Tokens e cores do tema
```

---

## 📏 As 5 Regras de Ouro

1. **Coesão Local (Colocation):** Tudo o que só é usado por uma feature nasce e mora dentro da pasta daquela feature. Só promova um componente para `DesignSystem/` ou um modelo para `Models/` quando **pelo menos 2 features distintas** precisarem dele.
2. **Núcleo Funcional Puro (`modelo.ts`):** Regras de validação, cálculos e máquinas de estado devem ser funções puras (sem banco, sem `fetch`, sem UI), permitindo testes unitários instantâneos.
3. **Fronteira Explícita (`publico.ts`):** Uma feature nunca importa arquivos internos (`repo.ts` ou subcomponentes privados) de outra feature; toda comunicação entre domínios passa pelo contrato público.
4. **Subpastas Sob Demanda (Sem Over-engineering):** Se uma feature tiver até ~8 arquivos, mantenha-os na raiz da própria feature. Só crie subpastas internas quando passar de 8–10 arquivos.
5. **Unificação da Camada Base:** Evite espalhar utilitários entre `lib/`, `utils/`, `helpers/` e `shared/` ao mesmo tempo. Padronize tudo em `src/compartilhado/` (ou `Services/` + `Utilities/` no iOS).

---

## 🔄 Refatoração Incremental (Strangler Pattern)

Para projetos existentes que precisam migrar sem interromper entregas:

1. **Crie um `tsconfig.modulos.json`:** Ative `strict: true`, `noUnusedLocals: true` e `noImplicitReturns: true` apenas sobre `src/modulos/**/*` e `src/compartilhado/**/*`, permitindo que o legado continue funcionando até ser migrado.
2. **Migre uma Feature por Vez:** Extraia primeiro `modelo.ts` + testes unitários, depois `repo.ts` + `api.ts`, e por fim transforme a rota em `src/app/` em uma casca fina.

---

## 🚀 Instalação e Uso

### No Google Antigravity (`skills.json`)
Adicione o caminho ao seu `~/.gemini/config/skills.json`:
```json
{
  "path": "/Users/upper/Documents/Projetos /1_I-A_Skills/feature-first-architecture/.agent/skills"
}
```

### Como Acionar (Com Confirmação)
No chat do agente, peça:
- *"Faça um diagnóstico deste projeto usando a skill `feature-first-architecture`"*
- *"Estruture este novo projeto seguindo a skill `feature-first-architecture`"*

---

## 📚 Documentação Adicional

- [`SKILL.md`](./SKILL.md) — Instruções operacionais da skill para o agente.
- [`docs/GUIA_DETALHADO.md`](./docs/GUIA_DETALHADO.md) — Exemplos práticos de código, contratos de módulo e checklist de migração.
