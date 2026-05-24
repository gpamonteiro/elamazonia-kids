# Code Standards — Amazônia Kids

> Convenções que linter NÃO pega. Coisas que linter já pega (formatting, semicolons, quotes) NÃO estão aqui — estão no Biome config.

## Nomenclatura

### Variáveis e funções
- `camelCase` — `getChildProfile`, `applyPreset`
- `PascalCase` pra types, interfaces, classes, componentes React: `ChildProfile`, `SceneCard`
- `SCREAMING_SNAKE` pra constantes globais: `MAX_SESSION_MINUTES`, `VOLUME_CAP_LUFS`
- Verbos pra funções, substantivos pra dados

### Entidades do domínio
- Singular pra tipos, plural pra tables: `type Scene` → `scenes` table
- Vocabulário canônico do glossary:
  - `presets` (CALMARIA, RITMO, FALA, EXPLORE, MOVIMENTO)
  - `conditions` (TEA, TDAH, SD, apraxia, PC, DI)
  - `scenes` (não `levels`, não `games`)
  - `children` (não `kids`, não `users` quando refere à criança)
  - `caregivers` (não `parents` — pode ser pai/mãe/avó/responsável)

### Files
- `kebab-case.ts` — `apply-preset.ts`, `scene-card.tsx`
- Schema files: `<context>.schema.ts` — `child.schema.ts`, `scene.schema.ts`
- Test: `*.test.ts` ao lado do arquivo testado

### Env vars
- `SCREAMING_SNAKE` sempre
- Prefix `NEXT_PUBLIC_` pra client-side (web)
- Prefix `EXPO_PUBLIC_` pra mobile client-side
- Validação obrigatória via `@t3-oss/env-nextjs` (web) + Zod (mobile)

## Padrões de erro

- **Única classe canônica:** `AppError` em `packages/api/src/errors.ts`
- NUNCA `throw new Error(...)` em código de negócio
- NUNCA criar nova class de erro sem aprovação
- Format: `throw new AppError({ code: "SCENE_NOT_FOUND", message: "Cena X não existe", httpStatus: 404 })`
- Cliente NUNCA recebe stack trace

## Padrões de log

- **Logger único:** `packages/api/src/lib/log.ts` (pino-based)
- Levels: `debug`, `info`, `warn`, `error`, `fatal`
- Format JSON em prod, pretty em dev
- **NUNCA logar PII** (nome criança, idade exata, email, telefone, condição)
- **NUNCA logar conteúdo de feedback dos pais** (texto livre)
- Logs com `request_id` pra correlação

## Padrões de validação (Zod)

- Schemas em `packages/validators/src/`
- Compartilhados client + server
- Nomes: `xSchema` (lowercase x) — ex: `childProfileSchema`
- Inferir tipos: `type ChildProfile = z.infer<typeof childProfileSchema>`
- Composição: extend > duplicate
- Sempre validar em fronteira (API input, env vars, form, telemetria opt-in)

## Padrões de UI

### Components
- Server components by default (Next 16 App Router)
- `"use client"` SÓ quando precisa: interactivity, hooks, state, browser APIs
- Compor: atoms (NativeWind/shadcn) → molecules (project-specific) → organisms (features) → screens
- NUNCA importar de `packages/db/` em components — só via tRPC

### Styling
- NativeWind v4 (mobile) + Tailwind v4 (web) utility-first
- shadcn/ui pra primitives web
- NÃO criar component custom até precisar 3+ usos (Rule of Three)
- Cores via design tokens (`packages/tokens/` futuro) — não hex inline

### Acessibilidade obrigatória
- WCAG 2.2 AA mínimo
- Sem flash >3 Hz (WCAG 2.1.1)
- Contraste alto opcional (toggle)
- Suporte switch access (PC + DI motor)
- Legendas pra qualquer áudio relevante pedagogicamente
- Touch targets ≥44pt (default) ou ≥88pt (MOVIMENTO preset)

## Padrões críticos do domínio

### Volume e som (CRÍTICO hiperacusia TEA)
- **Volume mestre cap −14 LUFS integrated** (constante `VOLUME_CAP_LUFS = -14`)
- **Sem transientes > 0 dBFS** (clip = bloqueio)
- **Fade-in/out ≥30ms em envelope de notas, ≥500ms em transições de cena**
- Helper centralizado: `packages/api/src/audio/safe-mix.ts` (a criar)

### BPM (entrainment)
- Range válido `60-120 BPM`
- Estável dentro da faixa do preset (não muda no meio da cena)
- Constante: `MIN_BPM = 60, MAX_BPM = 120`

### Sessão (anti-screen-time)
- Default 12 min, custom 5-15 dependendo preset
- Timer parental obrigatório
- "Tela calma" automática ao atingir cap
- AAP guideline: máx 1h/dia 2-5a

### Interação obrigatória (anti-Cocomelon)
- Sem vídeo passivo >90s sem requerer toque/voz/movimento
- Scene cuts máx 1 a cada 8s
- Sem dopamine-loops (sem rewards aleatórios estilo slot machine)
- Cada interação tem feedback determinístico

### Onboarding (12 perguntas)
- Schema canônico em `packages/validators/src/onboarding.schema.ts`
- Resultado mapeia pra `Preset` enum
- Pergunta 12 (telemetria opt-in) default **NÃO**

### IA rules engine
- Files em `packages/api/src/ai/rules/` (JSON)
- Cada regra: `{ condition, severity, parameter, value }`
- `RulesResolver` aplica em cascata: profile → condition rules → sensory override → individual learned preferences

### Privacy by design
- ChildProfile **NUNCA** sai do device Day 1
- Event log raw **NUNCA** sai
- Telemetria agregada SÓ se opt-in + k≥50 bucket
- DPIA documentado em `compliance/dpia.md` (a criar)

## Padrões de teste

### Vitest (unit + integration)
- `*.test.ts` ao lado do arquivo
- Estrutura: `describe("function/feature", () => { it("does X when Y", () => {...}) })`
- Tests devem (a) falhar antes da implementação, (b) testar behavior, (c) ter ≥1 assertion em dados reais
- **NUNCA** `expect(mockFn).toHaveBeenCalled()` como única assertion

### Coverage target
- **NÃO** medir coverage global
- **100% em:** ECA Digital compliance (consent, data export, deletion), 7 hard rules musicais, IA rules engine, onboarding
- Resto: ignorar coverage

## Não-padrões (anti-patterns)

| Anti-pattern | Por quê |
|---|---|
| `console.log` em produção | Use logger pino |
| Plain `Error()` em negócio | Use `AppError` |
| `any` type | Use `unknown` + narrow |
| Helper usado 1× | Inline (Rule of Three) |
| Volume > −14 LUFS | CRÍTICO TEA hiperacusia |
| BPM variável dentro de cena | Quebra entrainment |
| Flash >3 Hz | WCAG 2.1.1 violation |
| Vídeo passivo >90s | Anti-Cocomelon |
| `useEffect` pra fetching | Use tRPC + TanStack Query |
| 100% coverage como meta | Foque ECA/musical/IA coverage |
| FK cross-schema | Use ID reference |
| Mock DB em test integration | Use real Neon branch |
| Hard-code volume sem helper | Sempre `safeMix()` |
