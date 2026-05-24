# Architecture Context — Amazônia Kids

## Stack (resumo)

| Camada | Tecnologia | Função |
|---|---|---|
| Mobile principal | Expo SDK 55 + Expo Router + NativeWind v4 | App principal (crianças usam tablet/celular pais) |
| Web | Next.js 16 + App Router + Tailwind v4 + shadcn/ui | Admin pais + landing marketing + dashboard educadores |
| Backend | tRPC v11 | Type-safe contract web + mobile (compartilhado) |
| DB Day 1 | **100% on-device** (expo-sqlite + MMKV) | Storage local com criptografia (ECA Digital compliance) |
| DB servidor Mês 6+ | Postgres 18 (Neon free) + Drizzle 1.0 | Telemetria agregada anonimizada + auth |
| Auth | Better Auth (self-hosted) | Família (email + senha, magic link) |
| Audio engine | expo-av (mobile) + Howler.js (web) | Baixa latência |
| Animações | Lottie + Rive | Vetoriais leves (10-50kb cada) |
| IA Day 1 | Rules engine TypeScript on-device (~200 linhas) | Personalização por presets + perfil |
| IA Mês 6+ | Clustering agregado servidor (k≥50) | "Famílias com perfil parecido gostam de X" |
| IA Year 1+ | Thompson Sampling contextual on-device | Bandit adaptativo |
| IA Year 2+ | Federated learning (se >10k MAU) | TF Federated + differential privacy |
| Monorepo | Turborepo + pnpm 11 workspaces | Dev velocity |
| Audio licensing | Música ORIGINAL do músico amigo + voz da cantora amiga | 100% IP próprio |
| Observability | BetterStack (free 100k exceptions) + pino | Erros + logs |
| Hosting | Vercel (free Hobby dev, Pro $20 comercial) + Expo EAS Build (free 15 builds/mo) | $0 dev/MVP |

## System boundaries

- `apps/mobile/` — Expo app cliente (principal — crianças usam)
- `apps/web/` — Next.js landing + admin pais + dashboard educadores
- `packages/api/` — tRPC procedures, business logic. NUNCA importado em Client Component
- `packages/db/` — Drizzle schema + Postgres connection (Mês 6+)
- `packages/auth/` — Better Auth config
- `packages/validators/` — Zod schemas compartilhadas
- `packages/content/` — assets curados (músicas, animações, lore personagens)
- `context/` — docs vivas, agente lê antes de implementar
- `.claude/` — config Claude Code

## Storage model (privacy-first ECA Digital)

### Day 1: 100% on-device

```
┌──────────────────────────────────────────────────────────────┐
│  ChildProfile (PII)         ──┐                              │
│  Event log (raw timestamps)  ──┤  expo-sqlite + expo-secure-store │
│  Parent feedback             ──┤  (SQLCipher criptografado)  │
│  Computed metrics            ──┘                              │
│                                                              │
│  NUNCA sai do device:                                        │
│    - child_id, nome, idade exata                             │
│    - eventos brutos com timestamp                            │
│    - texto livre dos pais                                    │
└──────────────────────────────────────────────────────────────┘
```

### Mês 6+: telemetria agregada (opt-in default OFF)

```
┌──────────────────────────────────────────────────────────────┐
│  Agregador on-device (job diário)                            │
│  - bucketiza idade (3-4, 5-6, 7-8, 9-10, 11-12)             │
│  - bucketiza condição (TEA/TDAH/SD/apraxia/PC/DI)            │
│  - calcula completion_rate por scene_id                      │
│  - DESCARTA timestamps exatos                                │
└──────────────────────────────────────────────┬───────────────┘
                                               │ Só se opt-in (Q12)
                                               │ E k≥50 famílias no bucket
                                               ▼
┌──────────────────────────────────────────────────────────────┐
│  Backend (Brasil — SP, AWS sa-east-1 ou GCP southamerica-east1) │
│  Tabela: scene_metrics_aggregated                            │
│   (age_bucket, condition_bucket, scene_id,                   │
│    n, completion_rate, avg_time, week)                       │
│  - SEM IP, SEM device_id, SEM cookie                         │
│  - Logs deletados em 7 dias                                  │
│  - Backup criptografado, retenção 90d                        │
└──────────────────────────────────────────────────────────────┘
```

## Engagement signals (canonical list)

### Pode coletar (justificável + minimal)

| Signal | Como computa | Pra que serve |
|---|---|---|
| `time_on_task` (s) por cena | Δ scene_start ↔ scene_complete/abandon | Detecta dificuldade |
| `taps_per_minute` | count(taps)/min | Engajamento ativo |
| `completion_rate` | completed/started por scene_id | "Qual conteúdo funciona" |
| `replay_count` | quantas vezes re-joga | Favoritos implícitos |
| `abandonment_point` | timestamp+scene de session_end | Onde perde criança |
| `session_length_p50` | mediana últimas 14 sessões | Cap dinâmico de sessão |
| `inter_session_days` | dias entre sessões | Engajamento longo prazo |
| `hour_of_day_bucket` | manhã/tarde/noite (3 buckets) | Sugere melhor horário |
| `error_streak` | erros consecutivos | Trigger baixar dificuldade |

### NUNCA coletar

- Câmera pra emoção facial (proibido ECA + COPPA 2.0)
- Microfone passivo / voiceprint
- IMEI, Ad ID, fingerprint device
- Geolocalização precisa (GPS) — no máximo país via IP, descartado após sessão
- Cross-app tracking, SDKs ad-tech
- Inferência demográfica (gênero, raça, classe) — vetado CONANDA 245

## Onboarding (12 perguntas estruturadas, ≤6 min)

Preenchido pelo **adulto responsável**, com criança presente quando possível.

1. Idade criança (slider 2-12)
2. Condição principal (multi + "prefiro não dizer")
3. Perfil sensorial auditivo (0-5: hipo/típico/hiper)
4. Perfil sensorial visual (0-5)
5. Tempo de foco (<5min / 5-10 / 10-20 / >20)
6. Mobilidade fina (independente / com dificuldade / switch externo / olhar)
7. Comunicação verbal (fluente / palavras isoladas / pré-verbal / usa CAA)
8. Música que já adora (texto livre <100 chars opcional)
9. Música/som que incomoda (texto livre opcional)
10. Objetivo 3 meses (regular emoção / fala / atenção / motor / só diversão)
11. Quem usa com a criança (sozinha / pai-mãe / terapeuta / escola)
12. Aceito telemetria anônima agregada opt-in? (default NÃO)

→ Resultado mapeia automaticamente pra 1 dos 5 PRESETS.

## 5 PRESETS adaptativos

| Preset | Condições | BPM | Voz | Visual | Sessão |
|---|---|---|---|---|---|
| **CALMARIA** | TEA hiper, PC severa | 60-72 | Mezzo lenta, sem vibrato | Saturação 40%, fade 800ms | 8 min |
| **RITMO** | TDAH, DI leve | 96-112 | Articulada, sem letra em foco | Saturação 60%, fade 400ms | 5 min |
| **FALA** | Apraxia, SD não-verbal | 80-92 | MIT-like sílabas isoladas | Boca destacada, vogais visualizadas | 12 min |
| **EXPLORE** | TEA leve, DI moderada | 80-96 | Feminina padrão AABA | Padrão | 12 min |
| **MOVIMENTO** | PC moderada, DI motor | 80-100 | Rítmica comandos cantados | Alvos 88pt, contraste | 10 min |

## IA adaptativa — evolução planejada

| Fase | Quando | O que | Custo IA/mês 1k MAU |
|---|---|---|---|
| **Day 1** (mês 0-3) | Hoje | Rules engine + onboarding + storage on-device | R$0 |
| **Local Learner** (mês 3-6) | Pós-launch | Epsilon-greedy on-device (~150 linhas TS) | R$0 |
| **Clustering agregado** (mês 6-12) | Tração inicial | K-means servidor k=5-8 | R$200 |
| **Bandit contextual** (Year 1) | Validação | Thompson Sampling contextual | R$50 (Claude Haiku) |
| **Federated learning** (Year 2+) | Se >10k MAU | TF Federated + DP | R$3-8k |

## Sound design (parâmetros validados)

- **Volume mestre:** nunca > **−14 LUFS integrated** (hiperacusia TEA prevalência 41-86%)
- **Sem transientes > 0 dBFS** (cliques/pops são gatilho meltdown)
- **EQ:** filtro passa-baixa suave em **8 kHz** (corta agudos perigosos)
- **Envelope notas:** fade-in ≥30 ms (sem percussão abrupta)
- **Voz feminina mezzo-soprano (F0 ~220-330 Hz)** como narrador principal — alcance ótimo imitação infantil 2-8a
- **Voz masculina ocasional (barítono ~110-165 Hz)** pra personagens âncora (boto, jacaré)
- **Sem vibrato excessivo, sem trêmolo** (gatilho hiperacusia TEA)
- **Articulação lenta com vogais sustentadas** (modo MIT-like) em canções Apraxia/SD
- **Modo "energético-rítmico"** (sem caos) pra canções TDAH

## Architectural boundaries (modular monolith)

- `apps/mobile/` — UI only. Chama tRPC via @trpc/react-query
- `apps/web/` — UI only. Mesma regra
- `packages/api/` — business logic, server-only. NUNCA importado em Client Component
- `packages/db/` — Drizzle. Acessado SÓ via `packages/api/`
- `packages/validators/` — Zod schemas. ÚNICO compartilhado client+server

Sem multi-tenant complexo (não tem cidades múltiplas, não tem marketplace). Simples Day 1.

## Invariants (regras duras)

1. **TODA operação de PII fica on-device.** Sem exceção Day 1
2. **TODA telemetria que sai do device é anonimizada + agregada k≥50** (Mês 6+)
3. **Consent parental verificável Day 1** (ECA Digital + LGPD Art. 14)
4. **3 RED LINES** nunca quebradas (sem câmera/mic emoção, sem tempo-tela como métrica, sem dados terceiros sem consent)
5. **7 Hard Rules musicais** sempre respeitadas (volume, BPM, AABA, sem flash, sessão curta, interação ≤90s, voz humana fala/instrumental foco)
6. **Música é COADJUVANTE não substituto de terapia** — disclaimer claro
7. **Pais como gatekeeper, não babá digital** — timer, modo pai-junto, sugestão pausa

## Future expansion considerations

Designed pra suportar:
- **Mais personagens** (escolas amazônicas regionais distintas)
- **Mais ritmos** (lundu, marambiré, samba-canção, etc.)
- **Idioma extra** (Tupi-Guarani básico, Espanhol pra LATAM Year 3+)
- **Acessibilidade extra** (switch access avançado, voz pra comando)
- **Plataforma educador** completa (Mês 9+)
- **Federated learning** Year 2+ (se atingir 10k+ MAU)
- **Marketplace de composições da comunidade** (Year 3+, mediado pra evitar conteúdo agressivo)

NÃO design pra:
- Multi-tenant pesado (não é marketplace)
- Multi-cidade isolado (é app universal)
- B2B avançado Day 1 (vem Mês 6+)

## Hosting + custo

| Item | Day 1-MVP (free tier) | Production (1k MAU) | Production (10k MAU) |
|---|---|---|---|
| Vercel (web) | Hobby $0 (NÃO commercial) | Pro $20/mo | Pro $20/mo |
| Expo EAS (mobile builds) | Free 15 builds/mo | Starter $19/mo | Starter $19/mo |
| Neon Postgres | Free 100 CU-hr/mo, 0.5 GB | Launch $19/mo | Scale $69/mo |
| BetterStack | Free 100k exceptions | $29/mo | $29/mo |
| pino logs | $0 | $0 | $0 |
| IA APIs (rules Day 1) | $0 | $0 | $0 |
| Audio assets storage | Vercel Blob free 1GB | Pro included | Cloudflare R2 $1/mo |
| **TOTAL infra/mês** | **$0** | **~R$300-500** | **~R$1.500-3.000** |
