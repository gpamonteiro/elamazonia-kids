# AGENTS.md — elamazonia-kids

> **Amazônia Kids** — app musical interativo pra crianças com necessidades especiais (TEA, TDAH, Síndrome de Down, apraxia, paralisia cerebral, deficiência intelectual). Personagens fauna amazônica + ritmos paraenses (carimbó, brega marajoara) + IA adaptativa + 5 presets por perfil sensorial.
>
> **Foco imediato:** proposta Centelha 3 FAPESPA, deadline **11/jun/2026** (18 dias).
> Stack: Next.js 16, Drizzle 1.0, Postgres 18 (Neon), tRPC v11, Better Auth, Expo SDK 55, NativeWind v4.

## 1. Build & test commands

```bash
pnpm install          # primeira vez
pnpm dev              # dev server local (Turborepo --parallel)
pnpm test             # Vitest
pnpm lint             # Biome + ESLint (Next-specific)
pnpm typecheck        # tsc --noEmit em todos os packages
pnpm build            # Next.js build + packages
pnpm db:push          # sincroniza schema Drizzle ↔ DB (dev only)
pnpm db:studio        # GUI Drizzle Studio
pnpm validate         # roda typecheck + lint + test (pré-PR)
```

**Plan Mode no Windows:** use `Alt+M` ou comando `/plan` — `Shift+Tab` tá bugado no Windows ([#17344](https://github.com/anthropics/claude-code/issues/17344)).

## 2. Project structure

```
elamazonia-kids/
├── apps/
│   ├── web/                         # Next.js 16 admin pais + landing
│   ├── mobile/                      # Expo SDK 55 — app principal (Week 1-2)
├── packages/
│   ├── api/                         # tRPC v11 routers
│   ├── db/                          # Drizzle schema + Postgres
│   ├── auth/                        # Better Auth config
│   ├── validators/                  # Zod schemas
│   └── content/                     # músicas, animações, lore (curado)
├── context/                         # docs vivas — leia antes de implementar
│   ├── project-overview.md          # produto, visão, escopo
│   ├── architecture-context.md      # camadas, fronteiras, IA adaptativa, presets
│   ├── domain-glossary.md           # vocabulário música + neurodivergência
│   ├── code-standards.md            # convenções de código
│   ├── ai-workflow-rules.md         # red lines, Plan Mode, refactor
│   ├── progress-tracker.md          # estado VIVO — atualizar fim de sessão
│   └── decisions/                   # 15+ ADRs (metodologia + Amazônia Kids master + research science)
├── .claude/
│   ├── rules/                       # regras por path
│   ├── skills/                      # procedimentos
│   └── settings.json                # config Claude Code
└── ...
```

## 3. Code style

- **TypeScript strict** + `noUncheckedIndexedAccess` + `isolatedModules` + `verbatimModuleSyntax`
- **Validação Zod em TODA fronteira** (API, env, form, telemetria opt-in)
- **Sem `any`.** Use `unknown` + narrow. Lint `error`
- **Helpers só se usados 3+ vezes** (Rule of Three). Senão inline
- **Sem comentários supérfluos** — nome > comentário. Exceção: explicar *por quê* não-óbvio
- **Sem console.log** em produção — use logger
- **Dates: dayjs.** NÃO instalar date-fns/luxon/moment
- **HTTP: fetch nativo.** NÃO instalar axios

## 4. Git workflow

- **Trunk-based.** `main` sempre deployável
- **1 branch por PR.** Lifetime <48h. Tipos: `feat/`, `fix/`, `chore/`, `refactor/`, `docs/`, `test/`
- **Conventional Commits enforced.** 1 mudança lógica por commit
- **Manter `Co-Authored-By: Claude`** trailer em commits assistidos por IA
- **Squash on merge** pra main; preservar history dentro de PRs
- **CodeRabbit** revisa automático (free pra repo público — esse repo é público)

## 5. STOP-AND-READ rules (violações são bugs)

1. **Antes de criar helper, utility, hook ou class:** grep por funcionalidade similar e reporte. Default = extender, não criar.
2. **Antes de adicionar dep:** check package.json. Se já existe similar, use. Se realmente precisa nova, PARE e pergunte.
3. **Antes de criar arquivo novo:** justifique por que arquivo existente não pode hostar.
4. **Se requisito é ambíguo:** pergunte antes de adivinhar. Nunca invente comportamento de produto.
5. **Se corrigiu mesma issue 2×:** PARE. `/clear` e reescreva prompt.

## 6. Arquitetura privacy-first (CRÍTICO ECA Digital)

**ECA Digital (Lei 15.211/2025) está EM VIGOR desde 17/mar/2026.** Multa até R$50M.

### Storage Day 1
- **100% on-device** (expo-sqlite ou MMKV)
- Criptografado em repouso (expo-secure-store)
- ChildProfile (PII) NUNCA sai do device
- Event log raw NUNCA sai
- Parent feedback NUNCA sai
- Computed metrics NUNCA saem

### Telemetria (Mês 6+, opt-in default OFF, k-anonimity k≥50)
- Somente: `condition_bucket, age_bucket, scene_id, completion_rate, week`
- SEM child_id, SEM device_id, SEM IP, SEM timestamp exato
- Servidor: AWS sa-east-1 ou GCP southamerica-east1 (data residency BR)

### 3 RED LINES (NUNCA fazer)
1. ❌ Câmera/mic passivo pra detectar emoção — proibido ECA Digital + COPPA 2.0
2. ❌ Otimizar pra tempo de tela — ECA proíbe "recompensas por tempo gasto"
3. ❌ Compartilhar dados terceiros pra treinar IA externa sem consent específico

## 7. 7 Hard rules musicais (cientificamente validadas)

1. **Volume nunca >−14 LUFS; sem transientes >0 dBFS** (hiperacusia TEA 41-86% prevalência)
2. **BPM 60-120, estável dentro da faixa** (entrainment Effing 2022)
3. **Estrutura AABA repetitiva + previsibilidade temporal** (DI/SD Després 2024)
4. **Sem flashes >3 Hz, sem vermelho saturado movimento** (WCAG 2.1.1)
5. **Sessões 5-15 min, máx 1h/dia** (AAP + SBP)
6. **Interação obrigatória ≤90s — sem vídeo passivo longo** (anti-Cocomelon)
7. **Voz humana cantada quando objetivo é fala/imitação; instrumental quando foco** (Wan/Schlaug 2011)

## 8. 5 PRESETS adaptativos (mapeados de 5 dimensões onboarding)

| Preset | Condições | BPM | Voz | Sessão |
|---|---|---|---|---|
| **CALMARIA** | TEA hiper, PC severa | 60-72 | Mezzo lenta sem vibrato | 8 min |
| **RITMO** | TDAH, DI leve | 96-112 | Articulada sem letra em foco | 5 min |
| **FALA** | Apraxia, SD não-verbal | 80-92 | MIT-like sílabas isoladas | 12 min |
| **EXPLORE** | TEA leve, DI moderada | 80-96 | Feminina padrão AABA | 12 min |
| **MOVIMENTO** | PC moderada, DI motor | 80-100 | Rítmica comandos cantados | 10 min |

## 9. Plan Mode discipline

- **Obrigatório** pra mudança tocando >1 arquivo
- Pular SÓ pra typo/doc/1-line fix
- Windows: `Alt+M` ou `/plan` (não `Shift+Tab`)
- Plano deve listar: arquivos tocados, tests, migration impact, rollback
- Aprovar com `Ctrl+G` (editor) — não Enter cego

## 10. Red lines (humano SEMPRE)

- Auth, sessão, RBAC
- Pagamento (se houver Premium)
- Schema migration não-reversível
- Migration de produção (você roda, não agent)
- Secrets, keys, crypto
- Escolhas arquiteturais (DB, queue, auth provider, hosting)
- Decisões de conteúdo musical/pedagógico (passa pela cantora + musicoterapeuta consultor)
- Final approval PR money-path / privacidade infantil

## 11. Refactor policy

- Refactor = PR próprio, NUNCA bundle com feature
- Pré-requisito: código a refatorar **tem que ter testes**
- Prompt: *"Review este módulo e extraia lógica duplicada. NÃO mude comportamento. NÃO adicione abstrações exceto se usadas 3+ vezes."*
- Rejeitar PR de refactor que adicionou "helper" usado 1 vez

## 12. Read these first (workflow obrigatório)

Antes de implementar QUALQUER feature, leia em ordem:

1. `context/project-overview.md`
2. `context/architecture-context.md`
3. `context/domain-glossary.md`
4. `context/decisions/10-amazonia-kids-master.md` (BÚSSOLA)
5. `context/decisions/11-evidencia-cientifica-music-therapy.md` (parâmetros musicais por condição)
6. `context/decisions/12-ia-adaptativa-etica.md` (privacy + arquitetura IA)
7. `context/ai-workflow-rules.md`
8. `context/progress-tracker.md`

Depois **atualize `context/progress-tracker.md`** ao terminar a sessão.

## 13. Context7 MCP

USE Context7 quando:
- Implementando feature com lib externa (Next 16, Drizzle 1.0, Expo 55, Better Auth, etc.)
- Debugging que parece quirk de framework
- Antes de upgrade de versão

NÃO USE quando:
- Fix de typo / refator interno
- Lógica de negócio puramente interna

## 14. YOU MUST NOT

- ❌ Modificar arquivos em `drizzle/migrations/`. Use `pnpm db:migrate dev`
- ❌ Commit `.env`. Use `.env.example`
- ❌ Instalar packages sem aprovação
- ❌ Refatorar código não relacionado à task atual
- ❌ Marcar task done sem rodar `pnpm typecheck + pnpm lint + pnpm test`
- ❌ Rodar `git push --force` em main
- ❌ Skip pre-commit hook com `--no-verify`
- ❌ Bypass Plan Mode em mudança multi-arquivo
- ❌ console.log em código produção
- ❌ Coletar dados de criança sem consent parental verificável
- ❌ Quebrar nenhuma das 3 RED LINES de ECA Digital (§6)
- ❌ Quebrar nenhuma das 7 Hard Rules musicais (§7)
- ❌ Inventar comportamento de produto sem validar com cantora/musicoterapeuta

## 15. Centelha 3 — prazo crítico

**Deadline submissão: 11/jun/2026, 18h** (recomendado submeter dia 7 ou 8).

Pra entender o que precisa entregar, consultar `context/decisions/14-estrategia-redacao-proposta.md` (cronograma 18 dias + 17 perguntas Fase 1 + orçamento + vídeo pitch).

Atualizações de progresso da proposta ficam em `context/progress-tracker.md`.

## 16. Time criativo (importante pra Centelha)

- **Gabriel** (você) — Coordenador/CTO, ≥30h/sem
- **Amigo músico** — Diretor Musical, 15h/sem, formado teclado/piano + 5 instrumentos + gravações
- **Amiga cantora** — Designer pedagógica/locução, 15h/sem, vozes adaptáveis por perfil
- (Pós-aprovação Fase 2) **Bolsista CNPq DTI-B** — musicoterapeuta UBAM/ABMT como Especialista Visitante

**Confirmar:** nenhum dos 3 pode estar em outro projeto da Fase 2 do Centelha (regra item 3.1.3-d ELIMINA).
