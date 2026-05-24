# Decisão 14 — AMAZÔNIA KIDS Master Plan (consolidação 4 agents)

**Status:** Decidido — pronto pra execução
**Data:** 2026-05-24
**Aplica-se a:** projeto principal Centelha 3 (deadline 11/jun/2026 — 18 dias)
**Sobrescreve:** decision 13 (estratégia anterior MariNet/Pesca está OBSOLETA)

---

## 🎯 TL;DR — As 10 decisões consolidadas

1. **Projeto: AMAZÔNIA KIDS** — app musical interativo pra crianças com necessidades especiais (TEA, TDAH, SD, apraxia, PC, DI) com personagens fauna amazônica + ritmos paraenses + IA adaptativa
2. **Aprovação realista: 3-5%** (não 10-15% como estimado antes) — exige rigor cirúrgico
3. **ECA Digital JÁ EM VIGOR** desde 17/mar/2026 — compliance Day 1 obrigatório (multa R$50M)
4. **Estágio almejado Fase 1: "Protótipo conceitual"** — 1 nível jogável + mockups Figma em 14 dias
5. **5 PRESETS adaptativos:** CALMARIA, RITMO, FALA, EXPLORE, MOVIMENTO (baseado evidência científica)
6. **Time: 3 sócios** (você CTO + músico + cantora) + bolsista CNPq DTI-B (musicoterapeuta UBAM como "Especialista Visitante")
7. **Orçamento: R$80k subvenção + R$50k bolsa CNPq = R$130k total** (pró-labore exato 30% = R$24k, sem ultrapassar)
8. **Submeter dia 7 ou 8/jun (NÃO 11)** — congestão sistema
9. **6 cartas críticas:** SEMED Vigia + APAE Castanhal + UEPA Vigia + CAPSi Belém + 2 escolas Vigia (Madre Nazarena + Barão de Guajará)
10. **Pasta `elamazonia-kids/` no GitHub:** [gpamonteiro/elamazonia-kids](https://github.com/gpamonteiro/elamazonia-kids) — bootstrapada Day 1

---

## 🔥 Insights críticos novos

### O moat real (do que NINGUÉM mais tem)
1. **Música ORIGINAL composta por musicoterapeuta + músico** (não Spotify-license, não Suno genérico)
2. **5 presets adaptativos** que NENHUM competidor tem
3. **Identidade amazônica** — culturalmente única + alinha Vale Bioamazônico FAPESPA pós-COP30
4. **PT-BR nativo** + conformidade ECA Digital nativa
5. **Voz humana adaptável** pela cantora amiga (diferencial vs apps com IA de voz)

### Concorrente-template a vencer: JADE AUTISM (ES, Centelha 1, 2021)
- Hoje: 185k downloads, 179 países, 5k alunos
- Diferenciar EXPLICITAMENTE: Jade é generalista; Amazônia Kids é **cultural-amazônica + IA adaptativa + multi-NEE**
- Estudar [jadend.tech](https://www.jadend.tech/conheca-o-jade-edu)

### TIME-A negative result (Bieleninik 2017 JAMA) — usar com inteligência
**Não esconda:** o estudo mostra que música SEM ABA NÃO bate ABA. Use ASSIM no pitch:

> *"O Amazônia Kids NÃO se posiciona como substituto de terapia profissional. A evidência científica (Bieleninik 2017, TIME-A) demonstra que música isolada não substitui intervenções como ABA. Nosso app é COADJUVANTE: complementa terapias em curso, oferece estimulação musical adaptada para o tempo entre sessões profissionais, e democratiza acesso à música terapêutica para famílias do Norte. Geretsegger 2022 (Cochrane) e Sharda 2018 mostram que música ADICIONA valor mensurável."*

Isso protege juridicamente + mostra rigor + honestidade vende.

### Vigia tem ~1.300+ crianças neurodivergentes (TAM local)
- ~275-325 TEA + ~950 TDAH + ~70 SD
- **Vigia = 9ª cidade mais populosa região Belém metropolitana**
- TAM nacional: 14,4M PCDs (Censo 2022), 2,4M com TEA, 918k matrículas TEA em 2024 (+44%)

---

## 5 ESTUDOS PRIMÁRIOS pra citar (todos peer-reviewed)

1. **Geretsegger 2022 Cochrane** CD004381.pub4 — RR 1,22 melhora global pós-music therapy TEA
2. **Sharda 2018 Translational Psychiatry** — RCT 51 crianças, melhora comunicação + conectividade cerebral
3. **Wan/Schlaug 2011 PLoS ONE** — AMMT (Auditory-Motor Mapping Training) pra TEA não-verbal
4. **Bieleninik 2017 JAMA TIME-A** — citar com nuance pra posicionar app como coadjuvante
5. **Effing 2022 Frontiers Neurol** — NMT em Paralisia Cerebral

**+ 2 BR:**
- Apraxia infantil: *Brazilian J Dev* 2021
- Apraxia em foco: *Pró-Fono / SciELO*

---

## ARQUITETURA TÉCNICA (Day 1 → Year 2)

### Stack
- **Mobile:** Expo SDK 55 + Expo Router + NativeWind v4
- **Web (admin pais + landing):** Next.js 16 + App Router
- **Backend:** tRPC v11
- **Storage:** **100% on-device** Day 1 (SQLite/MMKV) — ECA Digital compliance
- **DB servidor (Mês 6+):** Postgres 18 (Neon) + Drizzle 1.0
- **Auth:** Better Auth (família)
- **Audio:** expo-av + Howler.js
- **Animações:** Lottie + Rive
- **IA Day 1:** Rules engine TypeScript puro (~200 linhas, on-device)
- **IA Mês 6+:** clustering agregado no servidor (k-anonimity k≥50)
- **IA Year 1+:** Thompson Sampling contextual on-device
- **IA Year 2+:** federated learning (se >10k MAU)
- **Hosting:** Vercel (web) + Expo EAS Build (mobile)

### Custo IA por escala
| Abordagem | 1k MAU | 10k MAU |
|---|---|---|
| Rules + local (Day 1) | R$0 | R$0 |
| + telemetria agregada | R$50 | R$200 |
| + clustering servidor | R$200 | R$500 |
| + LLM Claude Haiku por sessão | R$50 | R$500 |

**Recomendação:** Day 1-Mês 6 **zero LLM em runtime** (LLM só design-time). Mês 6+ Gemini Flash com cache.

### 3 RED LINES (NUNCA fazer)
1. ❌ Câmera/mic passivo pra detectar emoção (proibido ECA Digital + COPPA 2.0)
2. ❌ Otimizar pra tempo de tela (ECA proíbe "recompensas por tempo gasto")
3. ❌ Compartilhar dados terceiros pra treinar IA externa sem consent específico

---

## 7 HARD RULES de design musical (cientificamente validadas)

1. **Volume nunca >−14 LUFS; sem transientes >0 dBFS** (hiperacusia TEA 41-86%)
2. **BPM 60-120, estável dentro da faixa** (entrainment Effing 2022)
3. **Estrutura AABA repetitiva + previsibilidade temporal**
4. **Sem flashes >3 Hz, sem vermelho saturado movimento** (WCAG 2.1.1)
5. **Sessões 5-15 min, máx 1h/dia** (AAP + SBP)
6. **Interação obrigatória ≤90s — sem vídeo passivo longo** (anti-Cocomelon)
7. **Voz humana quando objetivo é fala/imitação; instrumental quando foco** (Wan/Schlaug)

---

## 5 PRESETS (sweet spot)

| Preset | Condições | BPM | Voz | Visual | Sessão |
|---|---|---|---|---|---|
| **CALMARIA** | TEA hiper, PC severa | 60-72 | Mezzo lenta, sem vibrato | Saturação 40%, fade 800ms | 8 min |
| **RITMO** | TDAH, DI leve | 96-112 | Articulada, sem letra em cena cognitiva | Saturação 60%, fade 400ms | 5 min |
| **FALA** | Apraxia, SD não-verbal | 80-92 | MIT-like sílabas isoladas | Boca destacada, vogais visualizadas | 12 min |
| **EXPLORE** | TEA leve, DI moderada | 80-96 | Feminina padrão, AABA | Padrão | 12 min |
| **MOVIMENTO** | PC moderada, DI motor | 80-100 | Rítmica, comandos cantados | Alvos 88pt, contraste | 10 min |

---

## ONBOARDING 5 dimensões (questionário pais ≤2 min)

| Dimensão | Modula |
|---|---|
| Sensibilidade auditiva (0-5) | Volume cap, EQ low-pass, vibrato off |
| Sensibilidade visual (0-5) | Saturação, contrast, animação ON/OFF |
| Nível verbal | Voz cantada lenta vs prosódia rica |
| Motor (toque preciso/amplo/switch) | Alvos 44pt vs 88pt vs scan mode |
| Andamento preferido | BPM 60-80 / 80-100 / 100-120 |

→ Mapeia automaticamente pra 1 dos 5 PRESETS

---

## ORÇAMENTO R$130k LINHA-A-LINHA

### Subvenção R$80k
- **Pró-labore sócios** (max 30%): **R$24k** (R$2k/mês ÷ 3 sócios)
- **Serviços terceiros PJ:** R$18k (ilustração 8k + sound 5k + QA 3k + UX 2k)
- **Material/software:** R$9k (Unity 1.2k + IA APIs 3k + Adobe 1.8k + Figma 1k + stores 2k)
- **Equipamento:** R$12k (MacBook M usado 8k + 2 tablets 4k)
- **Marketing:** R$6k
- **INPI:** R$2k
- **Incubadora PCT-Guamá:** R$4k
- **Contabilidade:** R$3k
- **Diárias:** R$2k

### Bolsa CNPq DTI-B R$50k
- 1 bolsista 12m (R$4.167/mês)
- **Sugerido: musicoterapeuta UBAM como Especialista Visitante**
- NÃO pode ser sócio que recebe pró-labore

### Contrapartida 5% (R$4k)
Sócios depositam antes 1ª parcela.

---

## CRONOGRAMA 12 MESES (M1-M12)

| Mês | Marco |
|---|---|
| M1 | SLU + conta BB + onboarding PCT-Guamá |
| M2 | 20 entrevistas pesquisa formativa |
| M3 | Design pedagógico co-assinado musicoterapeuta |
| M4 | Sprint 1 — boto jogável carimbó |
| M5 | Sprint 2 — arara brega + preguiça lundu |
| M6 | MVP fechado + testes 10 crianças |
| M7 | Beta 30 famílias TestFlight/Internal |
| M8 | V2 + dashboard educadores |
| M9 | Piloto 3 escolas (SEDUC + SEMEC + APAE) |
| M10 | Lançamento público lojas + evento PCT-Guamá |
| M11 | Tração: 1.500 downloads + 10 escolas + 1 contrato B2G |
| M12 | Relatório final + deck Série Pré-Seed |

---

## MODELO DE NEGÓCIO 3 STREAMS

| Stream | Pricing | Receita ano 2 |
|---|---|---|
| B2C Premium família | R$19,90/mês ou R$159/ano | R$159k (1.000 assinantes) |
| B2G/B2B Escolas | R$990 (30 alunos) ou R$2.490 (escola) | R$35k (20 contratos) |
| Filantropia/LBI | R$30-100k por marca | R$80k (2 patrocinadores) |
| **TOTAL ANO 2** | | **R$275k/ano** = sustentável |

LTV B2C ~R$280 (retenção 14m), CAC ~R$35 ASO+ads.

---

## 6 CARTAS DE APOIO CRÍTICAS (em ordem prioridade)

| # | Instituição | Contato | Prioridade |
|---|---|---|---|
| 1 | **SEMED Vigia** | semed@vigia.pa.gov.br — (91) 99627-6944 — **Josiclea Barata Pinheiro Palheta** | 🚨 visita presencial AMANHÃ |
| 2 | **APAE Castanhal** | R. Alameda Capanema 26, Castanhal — Facebook: apae.castanhal.5 | 🚨 começa email hoje (5-10 dias) |
| 3 | **UEPA Campus XVII Vigia** | uepa.br/content/vigia-campus-xvii | ⭐ vantagem geográfica + CEP próprio |
| 4 | **CAPSi Belém** (Arco-Íris) | (91) 3228-2997 | ⭐ |
| 5 | **Escola Madre Nazarena** (Vigia) | Via SEMED | ⭐ |
| 6 | **Escola Barão de Guajará** (Vigia) | Via SEMED | ⭐ |

**+ Bônus altamente recomendado:**
- **APEMTEPA** (Associação Musicoterapia Pará) — Vanessa Moraes Amado — **apemtepa@gmail.com**
- **ABMT Seção Pará** — Mt. Deyzianne Costa
- **AMA Pará** (Amigos dos Autistas)

Templates email + carta formal em [research-centelha/c-vigia-instituicoes-educacao-especial.md](research-centelha/c-vigia-instituicoes-educacao-especial.md)

---

## ROTEIRO 17 PERGUNTAS FASE 1

Detalhado em [research-centelha/d-estrategia-redacao-proposta.md](research-centelha/d-estrategia-redacao-proposta.md). Resumo:

- **P1 nome:** "Amazônia Kids — Aprendizagem Musical Adaptativa Amazônica pra Crianças Neurodivergentes"
- **P2 temática:** TIC + IA Educação Inclusiva + Patrimônio Cultural
- **P3 setor:** Educação primário + Saúde secundário
- **P4 problema:** dados IBGE + lacuna apps + eficácia musicoterapia + oportunidade
- **P5 solução:** app jogos musicais + fauna amazônica + ritmos paraenses + IA adaptativa + dashboard
- **P6 estágio:** PROTÓTIPO CONCEITUAL (construir em 14 dias)
- **P7 inovação:** produto + processo + modelo (3 frentes)
- **P9 ODS:** 4 primário + 3, 10, 11 secundários
- **P10 contribuição:** Lei 12.764 + LBI + ECA Digital + métricas realistas
- **P11 equipe:** storytelling honesto cobertura tripla
- **P13 funções:** Você 30h/sem + Músico 15h/sem + Cantora 15h/sem
- **P15-17:** vídeo + deck + logo (OPCIONAIS mas FAÇA)

---

## VÍDEO PITCH 180s — STORYBOARD

| Tempo | Cena |
|---|---|
| 0:00-0:15 | Você, fundo Vigia — abertura emocional |
| 0:15-0:35 | Dado IBGE + problema |
| 0:35-1:10 | **DEMO TELA (35s decisivos)** |
| 1:10-1:35 | Diferencial vs Otsimo, Jade |
| 1:35-2:00 | Mercado + modelo 3 streams |
| 2:00-2:30 | Equipe (3 falando em sequência) |
| 2:30-2:50 | Impacto + COP30/bioeconomia |
| 2:50-3:00 | Pedido + tela final |

**Trilha:** carimbó instrumental do músico (NUNCA banco genérico)
**Legendas:** embutidas
**Ensaiar:** 5x antes de gravar

---

## TOP 5 ANTI-PADRÕES (NÃO FAZER)

1. ❌ "App revolucionário/disruptivo/super-app"
2. ❌ TAM inflado ("200M pessoas")
3. ❌ Pró-labore >30%
4. ❌ MEI/EI (precisa SLU/Ltda/EPP)
5. ❌ Equipe duplicada (cada membro só em 1 projeto Fase 2)

**Bônus:** não citar IA sem mostrar como (seja específico mesmo técnico).

---

## CRONOGRAMA 18 DIAS DETALHADO

| Dia | Atividade |
|---|---|
| D1 (24/mai) | Ler edital PA 002/2026 3x + cadastrar trio Sistema Centelha PA |
| D2 (25/mai) | Definir título + rascunho P1-7 + reunião trio |
| D3 (26/mai) | 5 ligações famílias TEA/TDAH/SD pra quotes |
| D4 (27/mai) | Rascunho P8-14 + início mockups Figma |
| D5 (28/mai) | Mockups 4 telas + início trilha carimbó 30s |
| D6 (29/mai) | Protótipo navegável Figma + 1 cena Lottie + roteiro vídeo |
| D7 (30/mai) | **GRAVAÇÃO VÍDEO** |
| D8 (31/mai) | Edição vídeo + YouTube não-listado |
| D9 (01/jun) | Pitch deck PDF + logomarca |
| D10 (02/jun) | Revisão 17 respostas: cortar gordura + adicionar números |
| D11 (03/jun) | Submissão RASCUNHO (testar limites) |
| D12 (04/jun) | 3 leitores externos (técnico + leigo + educador) |
| D13 (05/jun) | Aplicar feedback + polish |
| D14 (06/jun) | Buffer imprevistos |
| **D15 (07/jun sexta)** | **🎯 SUBMISSÃO OFICIAL** |
| D16-17 (08-09/jun) | Confirmar email + imprimir PDF |
| D18 (10/jun) | Verificação final status "enviado" |

---

## REALIDADES PÓS-APROVAÇÃO

- Aprovação Fase 2: ~out/2026
- Constituição SLU: nov/2026
- **1ª parcela:** **jan-fev/2027** (gap 5 meses)
- Migração MEI → SLU: R$800-1.500, 10-15 dias (só após aprovação)
- Mantenha planilha gastos por rubrica desde dia 1 contratação (risco glosa)

---

## ESCADA FINANCEIRA PÓS-CENTELHA

| Programa | Valor | Janela |
|---|---|---|
| **FINEP Tecnova** | R$400k | sequencial após Centelha |
| **FINEP MPE Inovadora** | R$1,5M | sequencial |
| **BNDES Garagem 3ª Edição** | Aceleração + crédito (PCD + IA = pontuação extra) | Rolante |
| **MEC Programa Educação Inclusiva** | Compra via PAR | Aderência |
| **PDDE Interativo — Sala Recursos** | Repasse direto escolas SRM | Contínuo |
| **SEBRAE PA Startups Mais** | Mentoria + crédito | Contínuo |

---

## 🚀 PRÓXIMAS AÇÕES IMEDIATAS (próximos 3 dias)

1. **Hoje (D1):** baixar edital PA 002/2026 (URL https://drive.google.com/file/d/1V73eFrt8BWmACsK46IXb8JAJ6qKnYEOb/view) — ler 3x
2. **Hoje:** Eu populo AGENTS.md + context/* + decisions/ específicas do **elamazonia-kids** com toda essa info
3. **Amanhã (D2):** Reunião trio (você + músico + cantora) alinhar visão
4. **Amanhã:** Cadastrar 3 sócios no Sistema Centelha PA
5. **Quinta (D3):** Visita presencial SEMED Vigia (Josiclea Palheta) + emails APAE Castanhal, UEPA, CAPSi, APEMTEPA
6. **Sex-Sáb (D4-5):** Iniciar mockups Figma + músico começa trilha carimbó demo

---

## Arquivos de pesquisa relevantes neste workspace

- [research-centelha/a-evidencia-cientifica-music-therapy.md](research-centelha/a-evidencia-cientifica-music-therapy.md) — papers, parâmetros musicais, presets
- [research-centelha/b-ia-adaptativa-etica.md](research-centelha/b-ia-adaptativa-etica.md) — arquitetura IA + ECA Digital + compliance
- [research-centelha/c-vigia-instituicoes-educacao-especial.md](research-centelha/c-vigia-instituicoes-educacao-especial.md) — instituições + cartas + templates
- [research-centelha/d-estrategia-redacao-proposta.md](research-centelha/d-estrategia-redacao-proposta.md) — 17 perguntas + vídeo + orçamento + cronograma 18 dias

---

## Repositório

**Pasta limpa:** `c:\Users\Gabriel\Desktop\CODE\elamazonia-kids\`
**GitHub:** https://github.com/gpamonteiro/elamazonia-kids
**Status:** Day 1 foundation commitada (commit fc19e3b)
**Próximo:** popular AGENTS.md + context/* específicos do projeto + 6 decisions específicas com todo conteúdo dos 4 agents
