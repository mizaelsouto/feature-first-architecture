# 📘 Guia Detalhado — Vertical Slice & Feature-First Architecture (`feature-first-architecture`)

Este guia detalha como estruturar internamente cada fatia vertical (`features/<nome>/` ou `modulos/<nome>/`) em diferentes stacks (Full-Stack Web, Front-End/Mobile e Back-End/APIs), garantindo baixo acoplamento e alta coesão.

---

## 1. O Problema das Camadas Horizontais vs. A Solução Vertical Slice

### ❌ Abordagem Tradicional (Camadas Horizontais)
```text
src/
├── controllers/   (30 arquivos misturados)
├── services/      (30 arquivos misturados)
├── repositories/  (30 arquivos misturados)
├── components/    (80 componentes misturados)
└── hooks/         (40 hooks misturados)
```
- Para dar manutenção em **Pedidos**, você precisa abrir 5 pastas distantes.
- Alto risco de acoplamento acidental (qualquer service importa qualquer repository).

### ✅ Abordagem Vertical Slice / Feature-First
```text
src/
├── app/
├── design-system/
├── features/
│   ├── pedidos/       <-- Tudo sobre Pedidos está aqui
│   ├── pagamentos/    <-- Tudo sobre Pagamentos está aqui
│   └── catalogo/      <-- Tudo sobre Catálogo está aqui
├── models/
├── services/
└── utilities/
```
- Para entender, alterar, testar ou remover uma feature, você trabalha em **um único diretório**.

---

## 2. Contratos Internos em Projetos Full-Stack (`src/modulos/<feature>/`)

Cada pasta dentro de `src/modulos/` (ou `src/features/`) funciona como um micro-pacote com regras claras de dependência:

| Arquivo | Pode importar de | NÃO pode importar de | Papel |
| :--- | :--- | :--- | :--- |
| **`modelo.ts`** | Apenas tipos puros ou `utilities/` matemáticos | Banco de dados, `fetch`, React/UI, outros módulos | Regras de negócio, validações, cálculos e máquinas de estado. 100% testável em memória. |
| **`migracoes.ts`** | Tipo `Migracao` de `services/db` (`compartilhado/db`) | Qualquer código de runtime ou UI | Lista ordenada de migrações SQL idempotentes das tabelas do módulo. |
| **`repo.ts`** | `modelo.ts`, `services/db` | React/UI, framework HTTP, `api.ts` | Queries SQL/ORM parametrizadas e mapeamento banco → domínio. |
| **`api.ts`** | `modelo.ts`, `repo.ts`, `services/http`, `outroModulo/publico.ts` | Componentes de UI (`componentes/`) | Caso de uso no servidor, autenticação e orquestração de transações. |
| **`cliente.ts`** | `modelo.ts`, `services/http/cliente` | `repo.ts`, `api.ts`, driver de banco | Hooks e funções que o cliente usa para chamar `/api/<feature>`. |
| **`componentes/`** | `modelo.ts`, `cliente.ts`, `design-system/` | `repo.ts`, `api.ts`, componentes internos de outra feature | Telas e subcomponentes visuais específicos da feature. |
| **`publico.ts`** | `modelo.ts`, `repo.ts` (apenas leituras/ações autorizadas) | Subcomponentes privados | Contrato oficial da feature para consumo por outras features. |

---

## 3. Contratos Internos em Projetos Front-End & Mobile (`src/features/<feature>/`)

Para projetos React, Vue, React Native, Flutter ou Desktop/Mobile nativo:

```text
features/
└── captura/
    ├── CapturaScreen.tsx              # Componente raiz da tela/rota
    ├── BarraControlesCaptura.tsx      # Subcomponente coeso usado apenas em Captura
    ├── ContagemRegressivaView.tsx     # Subcomponente coeso usado apenas em Captura
    ├── IndicadorQualidade.tsx         # Subcomponente coeso usado apenas em Captura
    ├── useCaptura.ts                  # Hook/Store/ViewModel local da tela
    └── captura.service.ts             # Integração com API ou hardware específica da feature
```

### Quando mover algo para fora de `features/captura/`?
- **Para `design-system/`**: Somente quando um componente visual (botão, modal, badge, card base) passar a ser usado em **2 ou mais features** e não carregar regra de negócio de uma única tela.
- **Para `models/`**: Somente quando uma interface/entidade representar um conceito global que cruza várias features (ex: `UsuarioLogado`, `ConfiguracaoUnidade`).
- **Para `services/`**: Quando for um cliente base de infraestrutura (cliente HTTP autenticado, gerenciador de cache/storage, wrapper de WebSockets).
- **Para `utilities/`**: Quando for uma função pura genérica (formatação de CPF/CNPJ, cálculo de distância geográfica, manipulação de datas).

---

## 4. Contratos Internos em Projetos Back-End (`Node.js`, `Python/FastAPI`, `Go`)

Em um back-end Python (FastAPI) ou Node.js (Fastify/Nest/Express), a mesma estrutura se mantém:

```text
src/
├── app/
│   ├── main.py (ou server.ts)      # Inicializa o servidor e registra as rotas das features
│   └── config.py                   # Variáveis de ambiente validadas
├── features/
│   └── faturamento/
│       ├── router.py               # Endpoints REST da feature (/faturamento/*)
│       ├── usecase.py              # Fluxo da operação (chama model + repo + services)
│       ├── model.py                # Regras puras de cálculo/impostos + Schemas Pydantic/Zod
│       ├── repo.py                 # Queries SQL / SQLAlchemy / Prisma da feature
│       ├── public.py               # Funções expostas para outros módulos (ex: emitir_nota)
│       └── test_faturamento.py     # Testes unitários do model.py e de rota
├── models/                         # Contratos compartilhados (Paginacao, EventoDominio)
├── services/                       # Conexão DB, Redis, fila Celery/BullMQ, gateway de pagamento
└── utilities/                      # Validadores puros, hash, formatação
```

---

## 5. Checklist de Diagnóstico (STEP 0 — Antes de Refatorar)

Antes de sugerir ou executar uma mudança estrutural em qualquer repositório, o agente deve responder:

- [ ] **O projeto já usa fatias verticais (`features/` ou `modulos/`)?**  
  Se sim, **não** mova arquivos por estética. Apenas aponte eventuais arquivos órfãos (ex: utilitários duplicados entre `lib/` e `compartilhado/`).
- [ ] **Quantos imports serão quebrados/alterados?**  
  Meça com `grep -rn` antes de propor a mudança e informe o número exato ao desenvolvedor.
- [ ] **Existe suíte de testes e `typecheck` ativos?**  
  Em projetos sem testes ou typecheck, configure primeiro o `tsconfig.modulos.json` (ou linter equivalente) e testes das regras puras (`model` / `modelo.ts`) antes de mover pastas.
- [ ] **O usuário confirmou explicitamente?**  
  Nunca inicie a criação ou movimentação de diretórios sem resposta afirmativa direta do usuário.
