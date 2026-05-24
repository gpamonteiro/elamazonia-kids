# Research Note B — IA Adaptativa Ética + Arquitetura Privacy-First

**Agent:** IA adaptativa ética em apps infantis especiais
**Data:** 2026-05-24
**Status:** Completo

---

## 🚨 BOMBA REGULATÓRIA — ECA Digital JÁ ESTÁ EM VIGOR

**Correção crítica vs anotação anterior:**

- ❌ Eu disse: "ECA Digital vigência nov/2026"
- ✅ Realidade: **ECA Digital (Lei 15.211/2025) entrou em vigor em 17/03/2026**
- **JÁ ESTÁ VALENDO HÁ 2+ MESES**
- ANPD elegeu proteção infantil entre 4 temas prioritários 2026-2027

**Multas:** até **10% do faturamento BR** OU **R$50M por infração**

**Implicações pra Amazônia Kids:**
- Day 1 arquitetura **JÁ TEM QUE SER COMPLIANT** — não dá pra "fazer rápido e ajustar depois"
- Perfilamento comportamental de menores **praticamente PROIBIDO por padrão**
- Câmera pra emoção, mic passivo, biometria = **PROIBIDOS**
- Dark patterns (scroll infinito, autoplay, push excessivo, loot boxes) = **PROIBIDOS**
- Verificação de idade obrigatória (consentimento parental verificável)

## Arquitetura recomendada (Day 1 → Year 2)

```
┌──────────────────────────────────────────────────────────────┐
│  ONBOARDING (web/app, com pai/mãe presente)                  │
│  → 10-12 perguntas estruturadas                              │
│  → Gera ChildProfile {age, condition, sensoryProfile, goals} │
│  → Consentimento LGPD/ECA explícito + verificável            │
└────────────────────────────┬─────────────────────────────────┘
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  RULES ENGINE (100% on-device, JSON-based)                   │
│  IF condition=TEA + sensory=hypersensitive                   │
│    THEN low_freq_max=4kHz, transitions=slow, session_cap=10  │
│  IF condition=TDAH + age=6-8                                 │
│    THEN session_cap=8, micro_rewards=on, novelty_rate=high   │
└────────────────────────────┬─────────────────────────────────┘
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  EVENT BUS (on-device, SQLite/MMKV)                          │
│  scene_started, scene_completed, scene_abandoned             │
│  tap_count, time_on_task, replay                             │
└────────────────────────────┬─────────────────────────────────┘
                             ▼
┌──────────────────────────────────────────────────────────────┐
│  LOCAL AGGREGATOR (cron diário, on-device)                   │
│  Computa favoritos implícitos, cenas-fuga                    │
│  Atualiza pesos rules engine                                 │
│  Resumo semanal pra pai/mãe                                  │
└────────────────────────────┬─────────────────────────────────┘
                  ┌──────────┴──────────┐
                  ▼ (somente agregados) ▼
┌──────────────────────────────────────────────────────────────┐
│  TELEMETRIA ANÔNIMA (opt-in, k-anonimity k≥50)               │
│  Apenas: condition_bucket, age_bucket, scene_id,             │
│  completion_rate. SEM child_id, SEM device.                  │
└──────────────────────────────────────────────────────────────┘
```

**Stack solo dev:** React Native + Expo + `expo-sqlite`/MMKV + JSON rules engine (~200 linhas TS) + PostHog self-hosted ou Plausible. **Day 1 ZERO TensorFlow, ZERO ML.**

## Engagement signals — lista canônica

### Pode coletar (justificável + minimal)
| Signal | Como computa | Pra que serve |
|---|---|---|
| `time_on_task` por cena | Δ scene_start ↔ scene_end | Detecta dificuldade |
| `taps_per_minute` | count(taps)/min | Engajamento ativo |
| `completion_rate` | completed/started por scene_id | "Qual conteúdo funciona" |
| `replay_count` | quantas vezes re-joga | Favoritos implícitos |
| `abandonment_point` | timestamp+scene de session_end | Onde perde criança |
| `session_length_p50` | mediana últimas 14 sessões | Cap dinâmico |
| `inter_session_days` | dias entre sessões | Engajamento longo prazo |
| `hour_of_day_bucket` | manhã/tarde/noite (3 buckets) | Melhor horário |
| `error_streak` | erros consecutivos | Trigger baixar dificuldade |

### NUNCA coletar
- Câmera pra emoção facial (proibido)
- Microfone passivo / voiceprint
- IMEI, Ad ID, fingerprint device
- Geolocalização precisa
- Cross-app tracking, SDKs ad-tech
- Inferência demográfica (gênero, raça, classe) — vetado CONANDA 245

## Onboarding — questionário 12 itens (validados)

Preenchido pelo adulto responsável. Português claro, ícones. 4-6 min.

1. **Idade** (slider 2-12)
2. **Condição principal** (multi + "prefiro não dizer"): TEA / TDAH / SD / Apraxia / PC / Outras / Em investigação
3. **Perfil sensorial auditivo** (3 pontos): hipo / típico / **hiper** (90% TEA têm diferenças sensoriais — validado)
4. **Perfil sensorial visual** (mesma escala)
5. **Tempo de foco**: <5min / 5-10 / 10-20 / >20 → define `session_cap`
6. **Mobilidade fina**: independente / com dificuldade / switch externo / olhar
7. **Comunicação verbal**: fluente / palavras isoladas / pré-verbal / usa CAA (Avaz, Proloquo2Go)
8. **Música que já adora** (texto livre <100 chars opcional)
9. **Música/som que incomoda** (texto livre opcional)
10. **Objetivo 3 meses** (1 escolha): regular emoção / fala / atenção / motor / diversão → mapeia trilhas
11. **Quem usa com a criança**: sozinha / pai-mãe / terapeuta / escola → dashboards técnicos vs simplificados
12. **Telemetria anônima agregada opt-in?** (default **NÃO** conforme ECA Digital + COPPA 2.0)

Triagem opcional avançada:
- **M-CHAT-R/F** (PT-BR validado, sensibilidade 88,2%) pra 18-24m
- **SDQ** (Fleitlich & Goodman, validado BR) pra 4-12 anos
- NÃO forçar — apresentar como "extra"

## Regras de adaptação por condição (Day 1)

| Condição | Áudio | Visual | Sessão | Feedback | Conteúdo |
|---|---|---|---|---|---|
| **TEA + hipersensível** | Volume 50%, corte >4kHz, fade 800ms, sem sons súbitos | Paleta dessaturada, 0 piscadas | Cap 10min, aviso antes mudar | Reforço previsível | Sequências previsíveis, repetição livre |
| **TEA + hipossensível** | Volume 70%, percussão rica, vibração | Cores saturadas, animação suave | Cap 15min | Reforço sensorial | Loops longos, descobertas táteis |
| **TDAH** | Tempo moderado-rápido, variação | Estimulante mas não caótico | Cap 8min + "respirar 30s" (Pomodoro infantil) | Micro-recompensas 30-60s | Alta novidade, troca 2-3min |
| **Síndrome Down** | Andamento lento, articulação clara | Imagem grande + palavra (método global) | Cap 15min, ritmo da criança | Reforço caloroso | Repetição estruturada, vocab concreto |
| **Apraxia fala** | Modelagem clara, ritmo silábico, 3× repetir | Boca animada destacada | Cap 12min | Sucesso por aproximação (qualquer som = win) | Integração CAA, palavras curtas CV-CV |
| **Paralisia cerebral** | Volume/tempo ajustáveis | Alvos grandes ≥80dp, contraste | Sem cap rígido | Tolerância toque errado, dwell-click | Suporte switch access (1-2 switches), tempo resposta ampliado |

## Feedback parental — cadência

- **Pós-sessão** (opcional, 1 toque, 1×/dia): emoji 😊/😐/😣
- **Semanal** (domingo noite, push gentil): 3 perguntas, ≤90s
  1. Qual cena/música mais gostou? (top 5)
  2. Notou progresso? (sim/não/talvez + texto opcional)
  3. Algo que incomodou? (texto opcional)
- **Mensal:** dashboard passivo + 1 NPS
- **Trimestral opcional:** SDQ curta (25 itens, 5min) só se família optou

**NÃO fazer:** gamificação feedback, recompensas por preencher, push >2×/sem, perguntas longas

## Roadmap evolutivo

| Fase | Quando | O que | Custo IA/mês 1k MAU |
|---|---|---|---|
| **Day 1** (mês 0-3) | Hoje | Rules + onboarding + storage on-device | R$0 |
| **Local Learner** (mês 3-6) | Pós-lançamento | Epsilon-greedy on-device (~150 linhas TS) | R$0 |
| **Clustering agregado** (mês 6-12) | Tração inicial | K-means servidor k=5-8 sobre (condition, age, preferences) | R$200 |
| **Bandit contextual** (Year 1) | Validação produto | Thompson Sampling contextual | R$50 (Claude Haiku) |
| **Federated learning** (Year 2+) | Se >10k MAU | TF Federated + differential privacy | R$3-8k |

## Custo IA estimado (1k vs 10k MAU)

| Abordagem | 1k MAU | 10k MAU |
|---|---|---|
| Rules + local | R$0 | R$0 |
| + telemetria agregada | R$50 | R$200 |
| + clustering servidor | R$200 | R$500 |
| + LLM por sessão (Gemini Flash) | R$80 | R$800 |
| + LLM por sessão (Claude Haiku) | R$50 | R$500 |
| + LLM por sessão (Claude Sonnet) | R$540 | R$5.400 |
| Federated learning | R$3.000 | R$8.000 |

**Recomendação solo dev:** Day 1-Mês 6 **zero LLM em runtime**. LLM só **design-time** pra gerar conteúdo de cenas. Mês 6+ Gemini Flash com cache, 1 chamada/sessão, nunca PII.

## Top 3 RED LINES (NUNCA fazer)

1. **Câmera/mic passivo pra detectar emoção** — viola ECA + COPPA + bom senso
2. **Otimizar pra tempo de tela** — ECA proíbe "recompensas por tempo gasto"
3. **Compartilhar dados com terceiros pra treinar IA externa sem consent específico** — proibido COPPA 2.0 + CONANDA 245

## 5 Cases de referência

1. **Khan Academy Kids** — adaptive learning baseado em completion/accuracy. Híbrido on-device + cloud. **Lição: conteúdo bem tagueado > IA fancy.**
2. **Otsimo** (Turquia, TEA) — ABA + ML pra dificuldade adaptativa. **Lição: método terapêutico estabelecido + algoritmo leve. 80 jogos curados > 8000 gerados.**
3. **Duolingo ABC** — bandit pra notificações + softmax conteúdo. **Lição: implementar "recency decay".**
4. **Smile and Learn** (SD) — instruções visuais. **Lição: language-free design = inclusão default.**
5. **Mindstrong — FRACASSO instrutivo** — US$160M, fechou 2023. Tentou "digital phenotyping" passivo. **Lições:** (a) escalou cedo demais, (b) IA preditiva passiva gera ansiedade > valor, (c) foque em valor pedagógico imediato tangível.

## Compliance setup obrigatório (Day 1)

- **DPIA** (Relatório Impacto Proteção Dados) — template ANPD
- **Privacy policy** específica infantil — versão pais + versão criança ilustrada
- **DPO designado** (você no início, mas registre)
- **Data residency:** São Paulo (AWS sa-east-1, GCP southamerica-east1)
- **Audit log** acessos admin, retenção 6 meses
- **Botão "exportar dados" + "deletar tudo"** funcionando ≤15 dias

## 3 próximos passos concretos

1. **Esta semana:** rascunhar DPIA + privacy policy (pais + criança ilustrada). Barreira de entrada, não burocracia.
2. **Próximas 2 semanas:** implementar questionário onboarding (12 itens) + `RulesEngine` TS puro. Testar 3-5 famílias via WhatsApp ANTES de qualquer ML.
3. **Mês 1-3:** MVP com 8-12 cenas bem-curadas + rules + storage on-device + dashboard parental simples. **SEM TELEMETRIA SEQUER.** Valida que famílias amam o conteúdo antes de pensar em algoritmo.

## Princípio central

**IA é multiplicador de algo que já funciona.** Sem conteúdo musical excelente e culturalmente brasileiro pra essas crianças, nenhum bandit do mundo vai salvar. **Conteúdo bom primeiro → regras claras → privacidade radical → algoritmo por último.**

## Fontes principais

- [Senado — ECA Digital em vigor 17/03/2026](https://www12.senado.leg.br/noticias/materias/2026/03/17/eca-digital-para-protecao-on-line-de-criancas-e-adolescentes-entra-em-vigor)
- [Data Privacy Brasil — análise ECA Digital](https://www.dataprivacybr.org/eca-digital-entra-em-vigor-o-que-a-lei-preve-e-o-que-ainda-falta-regulamentar/)
- [5Rights Foundation — Brasil primeiro LATAM com age-appropriate](https://5rightsfoundation.com/brazil-is-first-latin-american-country-to-enshrine-age-appropriate-design-standards-into-law/)
- [CSMV — Resolução CONANDA 245/2024](https://www.csmv.com.br/resolucao-245-24-do-conanda-tratamento-de-dados-pessoais-de-criancas-e-adolescentes-no-ambiente-digital/)
- [Securiti — FTC COPPA 2.0 final rule](https://securiti.ai/ftc-coppa-final-rule-amendments/)
- [ICO — UK Children's Code](https://ico.org.uk/for-organisations/uk-gdpr-guidance-and-resources/childrens-information/childrens-code-guidance-and-resources/age-appropriate-design-a-code-of-practice-for-online-services/code-standards/)
- [Khan Academy — Khanmigo Interests personalization](https://blog.khanacademy.org/new-khanmigo-interests/)
- [Otsimo](https://otsimo.com/en/)
- [Duolingo — AI behind the meme](https://blog.duolingo.com/hi-its-duo-the-ai-behind-the-meme/)
- [STAT — Mindstrong demise](https://www.statnews.com/2023/02/06/mindstrong-demise-future-mental-health-care/)
- [INFORMS — Thompson Sampling educação](https://pubsonline.informs.org/doi/10.1287/ited.2025.0174)
- [PMC — M-CHAT-R/F validação PT-BR](https://pmc.ncbi.nlm.nih.gov/articles/PMC9273124/)
- [Monster Math — ADHD-Friendly App Design](https://www.monstermath.app/blog/adhd-friendly-app-design-what-to-look-for-and-what-to-avoid)
