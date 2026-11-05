# 📘 Guia Detalhado — Feature-First Architecture (`feature-first-architecture`)

Este documento detalha os contratos internos de cada arquivo dentro de uma fatia vertical (`Features/<Feature>/` no iOS ou `src/modulos/<feature>/` no Web/Full-Stack) e o checklist de decisão antes de qualquer refatoração.

---

## 1. Anatomia de um Módulo Full-Stack (`src/modulos/<feature>/`)

Cada pasta dentro de `src/modulos/` deve se comportar como um micro-pacote autossuficiente:

| Arquivo | Pode importar de | NÃO pode importar de | Papel |
| :--- | :--- | :--- | :--- |
| **`modelo.ts`** | Apenas tipos puros ou utilitários matemáticos puros | Banco de dados, `fetch`, React, Next.js, outros módulos | Regras de negócio, validações, cálculos e máquinas de estado. 100% testável em memória. |
| **`migracoes.ts`** | Tipo `Migracao` de `compartilhado/db` | Qualquer código de runtime ou UI | Lista ordenada de comandos SQL idempotentes (`001_...`, `002_...`) das tabelas do módulo. |
| **`repo.ts`** | `modelo.ts`, `compartilhado/db` | React, Next.js (`next/headers`), `api.ts` | Queries SQL parametrizadas e mapeamento linha → objeto de domínio. |
| **`api.ts`** | `modelo.ts`, `repo.ts`, `compartilhado/http`, `outroModulo/publico.ts` | Componentes React (`componentes/`) | Handlers HTTP do servidor, autenticação da requisição e orquestração de transações. |
| **`cliente.ts`** | `modelo.ts`, `compartilhado/http/cliente` | `repo.ts`, `api.ts`, `pg` | Funções e hooks que o navegador chama para conversar com as rotas `/api/<feature>`. |
| **`componentes/`** | `modelo.ts`, `cliente.ts`, `components/ui/` | `repo.ts`, `api.ts`, componentes internos de outro módulo | Telas e subcomponentes visuais específicos da feature. |
| **`publico.ts`** | `modelo.ts`, `repo.ts` (apenas leituras/ações autorizadas) | Subcomponentes internos | Fachada oficial do módulo para ser consumida por outros módulos. |

---

## 2. Anatomia de uma Feature Apple / SwiftUI (`Features/<Feature>/`)

Em projetos iOS/macOS com SwiftUI:

```text
Features/
└── Capture/
    ├── CaptureScreen.swift              # View raiz da rota/aba Capture
    ├── CaptureViewModel.swift           # (Opcional) @Observable se a lógica de tela for complexa
    ├── CaptureControlsBar.swift         # Subview coesa usada apenas em Capture
    ├── CaptureCountdownView.swift       # Subview coesa usada apenas em Capture
    ├── CaptureQualityIndicator.swift    # Subview coesa usada apenas em Capture
    └── SonarFeedView.swift              # Subview coesa usada apenas em Capture
```

### Quando mover algo para fora de `Features/Capture/`?
- **Para `DesignSystem/`**: Apenas quando um componente visual (ex: um badge, botão ou indicador) passar a ser usado em **2 ou mais features** (`Capture` e `SurveyViewer`) e não carregar semântica exclusiva de captura.
- **Para `Models/`**: Apenas quando uma `struct` ou `enum` representar um dado compartilhado entre múltiplas telas (ex: `DiveLogEntry`, `SurveySite`).
- **Para `Services/`**: Quando envolver I/O externo (Bluetooth, Sonar, Camera AVFoundation, API REST, SwiftData).

---

## 3. Checklist de Diagnóstico (STEP 0 — Antes de Refatorar)

Antes de sugerir ou executar uma mudança estrutural em qualquer repositório, responda:

- [ ] **O projeto já usa fatias verticais (`Features/` ou `src/modulos/`)?**  
  Se sim, **não** mova arquivos por estética. Apenas aponte eventuais arquivos órfãos (ex: utilitários duplicados entre `lib/` e `compartilhado/`).
- [ ] **Quantos imports serão quebrados/alterados?**  
  Meça com `grep -rn` antes de propor a mudança e informe o número exato ao desenvolvedor.
- [ ] **Existe suíte de testes e `typecheck` ativos?**  
  Em projetos sem testes ou typecheck, configure primeiro o `tsconfig.modulos.json` e testes das regras puras (`modelo.ts`) antes de mover pastas.
- [ ] **O usuário confirmou explicitamente?**  
  Nunca inicie a criação ou movimentação de diretórios sem resposta afirmativa direta do usuário.
