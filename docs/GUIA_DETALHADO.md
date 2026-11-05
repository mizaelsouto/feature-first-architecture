# 📘 Guia Detalhado — Os 5 Cenários da `feature-first-architecture`

Este documento detalha o protocolo interativo de decisão (`STEP 0`), a tabela de equivalência de nomenclatura (**Português vs. Inglês**) e os contratos de cada arquivo nos 5 cenários arquiteturais.

---

## 1. O Diálogo Interativo Obrigatório (`STEP 0`)

Antes de criar pastas ou mover arquivos, o agente deve apresentar ao usuário as 3 decisões fundamentais:

### Pergunta 1: Será um projeto Swift/Apple ou outro ambiente?
- **Se SIM (Projeto Swift / Apple):** Aplica o **Cenário 1** exatamente como no padrão oficial Apple (`App/`, `DesignSystem/`, `Features/<Feature>/`, `Models/`, `Services/`, `Utilities/`, `Preview Content/`).
- **Se NÃO:** Seleciona o **Padrão Universal de Arquitetura em Fatias Verticais** correspondente à natureza do sistema:
  - **Cenário 2:** Full-Stack Web / Monólito Modular (`Next.js`, `Nuxt`, `Remix`, `SvelteKit`)
  - **Cenário 3:** Front-End SPA ou Mobile Cross-Platform (`React`, `Vite`, `React Native / Expo`, `Flutter`)
  - **Cenário 4:** Back-End Puro / APIs & Microsserviços (`Node.js`, `Python / FastAPI`, `Go`, `Rust`)
  - **Cenário 5:** Sistemas de IA / Agentes Autônomos / RAG (`Python / TypeScript + LLMs / MCP`)

### Pergunta 2: Idioma da Estrutura (`Inglês` vs. `Português`)

| Conceito | Padrão em Inglês (`EN`) | Padrão em Português (`PT-BR`) |
| :--- | :--- | :--- |
| Entrada e Rotas | `App/` ou `src/app/` | `src/app/` |
| Design System | `DesignSystem/` ou `src/design-system/` | `src/components/ui/` ou `src/design-system/` |
| Fatias Verticais | `Features/<Feature>/` ou `src/features/<feature>/` | `src/modulos/<modulo>/` |
| Regras Puras de Domínio | `model.ts` / `model.py` / `Models/` | `modelo.ts` / `modelo.py` |
| Persistência da Fatia | `repo.ts` / `repo.py` | `repo.ts` + `migracoes.ts` |
| Contrato Público da Fatia | `index.ts` / `public.ts` | `publico.ts` |
| Camada Base Compartilhada | `Models/` + `Services/` + `Utilities/` (ou `src/shared/`) | `src/compartilhado/` (unificando `db/`, `http/`, `armazenamento/`, `utilitarios/`) |

### Pergunta 3: Modo de Execução
- **Apenas Diagnóstico / Plano (Dry-Run):** Analisa o repositório atual, calcula quantos imports seriam afetados e apresenta a árvore proposta sem modificar nenhum arquivo.
- **Novo Projeto (Greenfield):** Cria a estrutura inicial de pastas e contratos vazios.
- **Refatoração Incremental (Strangler Pattern):** Configura checagem estrita parcial (`tsconfig.modulos.json`) e migra 1 feature por vez mantendo todos os testes verdes.

---

## 2. Matriz de Responsabilidades por Arquivo (Cenários 2, 4 e 5)

| Arquivo na Fatia | Pode importar de | NÃO pode importar de | Responsabilidade |
| :--- | :--- | :--- | :--- |
| **`modelo.ts` / `model.py`** | Apenas tipos puros e utilitários matemáticos | Banco de dados, `fetch`, UI, outros módulos | Cálculos, validações, regras de negócio e máquinas de estado. 100% testável em memória. |
| **`repo.ts` / `repo.py`** | `modelo`, `services/db` (`compartilhado/db`) | Componentes de UI, rotas HTTP | Consultas SQL/ORM/Vetoriais exclusivas do domínio. |
| **`api.ts` / `usecase.ts`** | `modelo`, `repo`, `services/`, `outroModulo/publico` | Componentes visuais de UI | Orquestração do caso de uso, autenticação e transações. |
| **`prompts.py` + `tools.py`** *(Cenário 5)* | `model.py`, `services/llm` | Componentes visuais de UI | Prompts estruturados e ferramentas exclusivas do agente daquela fatia. |
| **`publico.ts` / `public.py`** | `modelo`, leituras/ações autorizadas de `repo`/`usecase` | Detalhes privados ou subcomponentes | Única porta de entrada permitida quando outro módulo precisa deste domínio. |

---

## 3. Checklist Anti-Regressão para Refatoração

- [ ] **O projeto já está em Vertical Slice?** Se sim, recomende manter como está e aponte apenas melhorias pontuais.
- [ ] **Quantos arquivos importam das pastas antigas?** Sempre execute `grep -rn` antes de propor mudanças.
- [ ] **`tsconfig.modulos.json` configurado?** Garanta `strict: true` nas novas fatias sem forçar a reescrita imediata do legado.
- [ ] **Testes das regras puras (`modelo`) rodando?** Extraia e teste `modelo.ts` antes de mover rotas ou componentes.
