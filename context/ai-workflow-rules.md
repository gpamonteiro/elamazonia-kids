# AI Workflow Rules — Amazônia Kids

Como você (agente IA) trabalha nesse repo. Violar = bug.

## 1. Plan Mode discipline

- **Obrigatório** pra qualquer mudança tocando >1 arquivo
- Pular SÓ pra typo / doc fix / 1-line fix
- **No Windows: use `Alt+M` ou `/plan`** (não `Shift+Tab` — [bug #17344](https://github.com/anthropics/claude-code/issues/17344))
- Plano deve listar:
  - Arquivos que vai criar (caminhos completos)
  - Arquivos que vai modificar (caminhos completos)
  - Arquivos que NÃO vai tocar
  - Tests adicionados/modificados
  - Migration impact (se houver)
  - 5-line rollback plan se quebrar
- Aprovar com `Ctrl+G` (abrir plano no editor) — não Enter cego

## 2. Workflow ideal (Explore → Plan → Implement → Verify)

### Step 1 — Explore
Antes de qualquer código:
1. Leia AGENTS.md
2. Leia context/ relevantes (project-overview, architecture, glossary)
3. **Especial:** leia decisions/10 (master), decisions/11 (musical), decisions/12 (IA ética)
4. Grep por funcionalidade similar — REPORTE o que existe
5. Leia arquivo de referência canônico se existe
6. PARE e espere aprovação

### Step 2 — Plan
7. Liste arquivos tocados, tests, migration, rollback
8. PARE e espere aprovação

### Step 3 — Implement
9. Code seguindo o plano APROVADO
10. NÃO desviar do plano sem reportar

### Step 4 — Verify
11. Rode `pnpm typecheck && pnpm lint && pnpm test`
12. Self-review contra arquivo de referência
13. Reporte: passou tudo? algum desvio do plano?

## 3. Red lines (humano SEMPRE escreve ou pair-write)

NÃO auto-implementar:

1. **Autenticação / autorização / sessão / RBAC**
2. **Conteúdo musical novo** (passa pela cantora + musicoterapeuta consultor)
3. **Conteúdo visual de personagens** (passa pelo músico/cantora pra validação cultural)
4. **Mudanças de privacidade / ECA Digital compliance**
5. **Schema change não-trivialmente reversível**
6. **Migration de produção** (você prepara, humano roda)
7. **Secrets, keys, signing, crypto**
8. **Escolhas arquiteturais** (DB, queue, auth provider, hosting, IA architecture)
9. **Final approval** de PR tocando os acima

Pra esses, apresente proposta e ESPERE explícita aprovação.

## 4. 3 RED LINES privacy (NUNCA fazer)

1. ❌ Câmera ou microfone passivo pra detectar emoção/estado da criança
2. ❌ Otimizar pra tempo de tela como métrica de sucesso (ECA Digital proíbe)
3. ❌ Compartilhar dados com terceiros pra treinar IA externa sem consent específico

## 5. 7 HARD RULES musicais (NUNCA quebrar)

1. Volume sempre `≤ −14 LUFS integrated` (helper `safeMix()`)
2. BPM `60-120`, estável dentro da faixa do preset
3. Estrutura AABA repetitiva + previsibilidade temporal
4. Sem flashes >3 Hz, sem vermelho saturado movimento (WCAG 2.1.1)
5. Sessões 5-15 min, máx 1h/dia
6. Interação obrigatória ≤90s — sem vídeo passivo longo
7. Voz humana cantada quando objetivo é fala/imitação; instrumental quando foco

## 6. Anti-padrões que causam bug silencioso

### Reinvented utility
Antes de criar helper/util/hook/class, **GREP** por funcionalidade similar.
Default: extender existente. Criar novo = última opção.

```bash
# Antes de criar formatBPM(), busque:
grep -r "BPM\|bpm\|tempo" packages/api/src/
grep -r "format.*Sound\|format.*Volume" packages/api/src/
```

### Refactor creep
- Toque APENAS arquivos no plano
- Refactor é PR próprio, nunca bundle com feature

### Test gaming
- `expect(mockFn).toHaveBeenCalled()` como única assertion = REJECT

### Dependency drift
- NÃO instale package sem aprovação explícita
- Se REALMENTE precisa novo, PARE e justifique

### Helpers usados 1×
- Inline. Rule of Three: extrair só se usado 3+ vezes

### Hallucinated imports
- Sempre rode `pnpm typecheck` antes de declarar feature done

### Crossing architectural boundaries
- `apps/` NUNCA importa `packages/db/` direto
- `packages/validators/` é o ÚNICO compartilhado client+server

## 7. Session discipline

- **`/clear` entre tasks não relacionadas** sempre
- **`/compact <instruction>`** quando thread atual ainda importa
- Se corrigiu mesma issue 2× na sessão → `/clear` e reescreva prompt
- Atualizar `context/progress-tracker.md` no FIM de cada sessão significativa

## 8. Use Context7 MCP

Quando implementar feature com lib externa:
- Next.js 16, Drizzle 1.0, Expo SDK 55, Better Auth, NativeWind v4, tRPC v11, Lottie, Howler.js
- Sua training data pode estar desatualizada

NÃO use Context7:
- Lógica de negócio puramente interna
- Fix de typo / doc / refator interno
- Sessão já consultou aquela lib hoje

## 9. Stop-and-read rules (críticas)

Antes de implementar:

1. **Antes de criar helper:** grep e reporte. Default extender.
2. **Antes de adicionar dep:** check package.json. PARE se precisa nova.
3. **Antes de criar arquivo:** justifique por que arquivo existente não pode hostar.
4. **Se requisito ambíguo:** pergunte. Não invente comportamento de produto.
5. **Se corrigiu mesma issue 2×:** PARE. /clear. Reescreva.

## 10. Money-path / Privacy-path checklist (obrigatório pra qualquer mudança nessas áreas)

Pra `packages/api/src/privacy/` ou `packages/api/src/payments/` (se Premium futuro):

- [ ] Audit log entry escrito
- [ ] Transaction Postgres atomicidade
- [ ] Tests cobrindo: success, failure, retry, idempotent retry
- [ ] PII (CPF, nome criança, condição) não logada
- [ ] Erro NÃO suprimido (throw, audit)
- [ ] `/security-review` rodado
- [ ] DPIA atualizado se aplicável
- [ ] Consent parental verificável se coleta dado

## 11. Conteúdo musical/visual — validation flow

NUNCA criar/publicar conteúdo sem:

1. Aprovação **músico amigo** (composição/instrumental)
2. Aprovação **cantora amiga** (voz/fala)
3. Aprovação **musicoterapeuta consultor** (validação clínica) — quando contratado
4. Teste contra 7 Hard Rules musicais (script automatizado)
5. Teste de acessibilidade (WCAG 2.2 AA)

## 12. Update progress-tracker.md ao final

Toda sessão significativa, atualize `context/progress-tracker.md`:

```
**Última atualização:** YYYY-MM-DD (autor: claude/humano)

## Done recently
- [data] Implementado X (PR #N)

## In progress
- [feature] [estado] [bloqueios]

## Next 1-3 sessions should
1. Próxima ação concreta
```

## 13. Forbidden actions

- ❌ `git push --force` em main
- ❌ Skip pre-commit hook com `--no-verify`
- ❌ Rodar migration de produção de session
- ❌ Instalar dep sem aprovação
- ❌ Modificar arquivos em `drizzle/migrations/` (use `pnpm db:migrate`)
- ❌ Commit `.env`
- ❌ Refator código fora do scope
- ❌ Marcar task done sem rodar typecheck + lint + test
- ❌ Bypass Plan Mode em mudança multi-arquivo
- ❌ Modificar `context/decisions/*` (são ADRs históricos)
- ❌ Quebrar nenhuma das 3 RED LINES privacy
- ❌ Quebrar nenhuma das 7 HARD RULES musicais
- ❌ Publicar conteúdo musical/visual sem validation flow (§11)
