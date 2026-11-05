---
name: feature-first-architecture
description: >-
  Arquitetura Feature-First (Vertical Slice) com DesignSystem e Core Compartilhado
  para novos projetos ou refatoração (SwiftUI/iOS, Next.js Full-Stack, React/Mobile).
  REGRA DE ACIONAMENTO (CONFIRMAÇÃO OBRIGATÓRIA): NUNCA acione nem aplique esta
  skill de forma deliberada ou automática. O agente DEVE apenas sugerir seu uso ou,
  quando solicitado, apresentar primeiro um plano de diagnóstico e aguardar a
  confirmação explícita do usuário antes de criar ou mover qualquer arquivo.
---

# 🏗️ Skill: Arquitetura Feature-First / Vertical Slice (`feature-first-architecture`)

Padrão arquitetural pragmático que organiza o código por **funcionalidade/domínio (Features / Módulos)** em vez de camadas técnicas horizontais (`controllers/`, `views/`, `services/` globais), mantendo um **DesignSystem** isolado e uma camada enxuta de **Serviços/Utilitários Compartilhados**.

---

## 🛑 PROTOCOLO DE SEGURANÇA: PROIBIDO ACIONAMENTO DELIBERADO (STEP 0)

> [!IMPORTANT]
> **Esta skill NUNCA deve ser executada de forma autônoma, silenciosa ou deliberada pelo agente.**

Sempre que você identificar que um projeto novo ou existente poderia se beneficiar desta arquitetura — ou mesmo quando o usuário perguntar sobre organização de pastas:

1. **NÃO crie pastas, NÃO mova arquivos e NÃO altere imports imediatamente.**
2. **Faça primeiro o Diagnóstico de Custo × Benefício:**
   - Se o projeto já possui uma estrutura funcional ou já segue fatias verticais (ex: `src/modulos/`), **recomende NÃO refatorar** apenas por estética, apontando apenas eventuais melhorias pontuais.
   - Se for um projeto novo ou uma refatoração necessária, apresente a **Árvore Proposta** e a **Estimativa de Impacto** (quantos arquivos/imports serão afetados).
3. **Peça Confirmação Explícita:**
   - Pergunte claramente ao usuário: *"Deseja que eu aplique a estrutura `feature-first-architecture` agora?"* (ou use `ask_question` com as opções de escopo).
   - **Aguarde o "Sim" explícito do usuário** antes de executar qualquer alteração no sistema de arquivos.

---

## 🧭 Anatomia da Arquitetura (Mapa de Pastas)

A estrutura divide o projeto em **4 zonas claras**:
1. **Entrada & Roteamento (`App` / `src/app`)**: Ponto de entrada, ciclo de vida e rotas finas.
2. **Design System (`DesignSystem` / `src/components/ui`)**: Componentes visuais puros, tokens, cores e tipografia, sem regras de negócio.
3. **Funcionalidades (`Features` / `src/modulos`)**: O coração do produto. Cada subpasta é um domínio autossuficiente.
4. **Núcleo Compartilhado (`Models`, `Services`, `Utilities` / `src/compartilhado`)**: Infraestrutura transversal (banco, rede, storage, helpers puros) e entidades compartilhadas por múltiplas features.

---

### 📱 Variante 1: Nativo Apple (iOS / macOS — Swift & SwiftUI)

Ideal para apps Xcode que buscam alta coesão e facilidade futura de extração em módulos do Swift Package Manager (SPM):

```text
NomeDoApp/
├── App/                        # @main, configuração raiz, roteamento global e ciclo de vida
├── DesignSystem/               # Tokens, cores, tipografia SF, botões e componentes HIG reutilizáveis
├── Features/                   # Fatias verticais por tela/fluxo do produto
│   ├── Capture/
│   │   ├── CaptureScreen.swift           # Tela principal da feature
│   │   ├── CaptureControlsBar.swift      # Subcomponente local da feature
│   │   ├── CaptureCountdownView.swift    # Subcomponente local da feature
│   │   └── SonarFeedView.swift           # Subcomponente local da feature
│   ├── DiveLog/
│   ├── Settings/
│   ├── SiteList/
│   └── SurveyViewer/
├── Models/                     # APENAS entidades de domínio que cruzam 2+ features
├── Services/                   # Infraestrutura: API, Bluetooth, sensores, persistência, CoreData/SwiftData
├── Utilities/                  # Extensions, formatadores e funções puras sem estado
├── Preview Content/            # Mocks e assets exclusivos para Xcode Previews
└── Assets.xcassets             # Ícones, imagens e cores do bundle
```

---

### 🌐 Variante 2: Web & Full-Stack (Next.js App Router / TypeScript)

Inspirada na arquitetura validada do **RecicleUai_2026** (Monólito Modular Full-Stack em fatias verticais):

```text
src/
├── app/                        # Casca fina do Next.js (roteamento páginas + rotas HTTP)
│   ├── (rotas)/page.tsx        # Importa e renderiza a tela de src/modulos/<feature>/componentes
│   └── api/<feature>/route.ts  # Repassa a requisição para src/modulos/<feature>/api.ts
├── components/
│   ├── ui/                     # Design System (BottomSheet, SegmentedControl, EstadoVazio, etc.)
│   └── layout/                 # AppShell, Footer, navegação global
├── modulos/                    # (ou features/) — Cada pasta é um domínio Full-Stack isolado
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
    ├── localizacao/            # GPS, cálculo de distância (Haversine), geocodificação
    └── tema.ts                 # Tokens e cores do tema
```

---

## 📏 As 5 Regras de Ouro (Governança do Código)

1. **Regra da Coesão Local (Colocation):**
   Tudo o que só é usado por uma feature (`Capture` ou `ofertas`) nasce e mora dentro da pasta daquela feature. Só promova um componente para `DesignSystem/` ou um modelo para `Models/` quando **pelo menos 2 features distintas** precisarem dele.
2. **Núcleo Funcional Puro (`modelo.ts` / Domain Models):**
   Regras de validação, cálculos, máquinas de estado e formatação de domínio devem ficar em funções puras (sem banco de dados, sem `fetch`, sem UI). Isso permite testes unitários instantâneos em milissegundos.
3. **Fronteira entre Features (`publico.ts`):**
   Uma feature não deve importar detalhes internos (`repo.ts` ou subcomponentes privados) de outra feature. Quando houver comunicação entre domínios, exponha apenas o necessário via contrato público.
4. **Subpastas Sob Demanda (Sem Over-engineering):**
   Se uma feature tiver de 3 a 8 arquivos, mantenha-os na raiz da própria feature (como em `Features/Capture/`). Só crie subpastas internas (`componentes/`, `viewmodels/`) quando a feature ultrapassar ~8–10 arquivos.
5. **Unificação de `Services` + `Utilities` (Evitar pastas duplicadas):**
   Em projetos TypeScript/JS, escolha **uma** nomenclatura para a camada base (`src/compartilhado/` ou `src/core/`). Evite manter `src/lib/`, `src/utils/`, `src/helpers/` e `src/shared/` simultaneamente.

---

## 🔄 Guia de Refatoração Segura (Para Projetos Existentes)

Quando o usuário **confirmar explicitamente** que deseja refatorar um projeto legado para esta estrutura, siga estritamente este fluxo incremental:

### Passo 1: Mapeamento sem quebra
- Liste os domínios do projeto e identifique o acoplamento atual (`grep` de imports).
- Nunca tente mover o projeto inteiro em um único commit se houver mais de 20 arquivos.

### Passo 2: Configuração de Typecheck Estrito por Fatia (`tsconfig.modulos.json`)
Em projetos TypeScript legados (onde `strict: false` ainda existe na raiz), crie um `tsconfig.modulos.json` que herda do `tsconfig.json` principal mas ativa rigor máximo apenas para os módulos novos/migrados:
```json
{
  "extends": "./tsconfig.json",
  "compilerOptions": {
    "strict": true,
    "noUnusedLocals": true,
    "noImplicitReturns": true
  },
  "include": [
    "src/modulos/**/*.ts",
    "src/modulos/**/*.tsx",
    "src/compartilhado/**/*.ts",
    "src/compartilhado/**/*.tsx"
  ]
}
```

### Passo 3: Extração Fatia por Fatia (Strangler Pattern)
1. Escolha **uma** feature por vez.
2. Extraia primeiro as regras puras para `modelo.ts` e crie os testes unitários.
3. Mova os componentes e repositórios da feature.
4. Transforme a rota original (`src/app/.../page.tsx`) em uma casca fina que apenas chama a nova feature.
5. Rode `npm run typecheck` e os testes antes de avançar para a próxima feature.
