# Project Overview — Amazônia Kids

## Resumo (1 parágrafo)

**Amazônia Kids** é uma plataforma musical interativa para crianças com necessidades especiais (TEA, TDAH, Síndrome de Down, apraxia da fala infantil, paralisia cerebral, deficiência intelectual). Personagens da fauna amazônica (boto rosa, ararauna, jacaré, sapo cururu) cantam músicas autorais inspiradas em ritmos paraenses (carimbó, brega marajoara, lundu) compostas pelo músico do time. A cantora do time grava vozes adaptáveis a 5 perfis sensoriais distintos. IA adaptativa Day 1 (rules-based on-device) personaliza progressão por TIPO de necessidade e por interação individual da criança. Privacy-first nativo (ECA Digital + LGPD compliant). Solo dev + 2 criativos. Foco imediato: proposta Centelha 3 FAPESPA (deadline 11/jun/2026).

## Goals

1. **Submeter proposta Centelha 3 até 11/jun/2026** com demo navegável + vídeo pitch 3min
2. **Se aprovado:** entregar MVP em 12 meses (R$130k) — 4 personagens + 12 músicas autorais + 5 presets adaptativos + dashboard pais/educadores
3. **Pilotar com 3 escolas Vigia/PA** (1 SEDUC + 1 SEMEC + 1 APAE) no mês 9
4. **Lançar público lojas mês 10** com meta de 1.500 downloads e 1 contrato B2G assinado mês 11
5. **Year 2:** sustentabilidade R$275k/ano (3 streams: B2C família + B2G escolas + filantropia LBI)
6. **Year 3+:** expansão nacional + parcerias institucionais (MEC, BNDES Garagem, PDDE Interativo)

## Personas

| Persona | Quem é | Necessidade |
|---|---|---|
| **Criança 2-12 anos com necessidade especial** | TEA/TDAH/SD/apraxia/PC/DI | Estimulação musical adaptada, lúdica, segura, sem overstimulação. Ferramenta que respeita seu perfil sensorial |
| **Pai/mãe/responsável** | Geralmente classe C/D (Norte sub-atendido por terapeutas) | Apoio entre sessões profissionais. Sem culpa de "ser babá digital". Insights sobre evolução |
| **Terapeuta / fonoaudióloga** | Profissional educação especial / saúde | Ferramenta complementar com base científica. Dashboard pra acompanhar paciente. Validação UBAM |
| **Professor AEE escola** | Sala Recursos Multifuncionais | Recurso pra usar com aluno especial. Plano de aula apoiado. Métricas progresso |
| **Diretor escola APAE/SEDUC** | Gestor educacional | Solução acessível pra alunos especiais, validada cientificamente, brasileira |

## Core user flows

### Flow 1 — Onboarding família (primeira vez)
1. Pai/mãe baixa app
2. Cria conta família (Better Auth, email + senha)
3. Cadastra perfil criança: 12 perguntas estruturadas (≤6 min)
4. App mapeia perfil → 1 dos 5 PRESETS adaptativos
5. Consentimento ECA Digital + LGPD explícito + verificável
6. Tutorial 60s pais + 30s criança
7. Primeira sessão recomendada (cena CALMARIA padrão)

### Flow 2 — Sessão musical interativa
1. Criança abre app (pai presente)
2. Vê os personagens da fauna (sem barulho súbito — tela calma)
3. Escolhe personagem (Téo Boto Rosa, Lulu Ararauna, Tião Jacaré, Cuca Sapo)
4. Inicia cena adaptada ao seu PRESET (BPM, voz, visual ajustados)
5. Interage tocando → personagem reage → toca nota → animação suave
6. Sessão dura 5-15 min (depende do PRESET)
7. Timer parental aciona "tela calma" no fim
8. Opcional: pai dá feedback rápido (emoji 😊/😐/😣)

### Flow 3 — Dashboard pai/mãe (semanal)
1. Resumo semanal: qual cena mais gostou, tempo total, padrões
2. Sugestões de progressão (próximo nível?)
3. Pergunta opcional 3 itens (≤90s): preferência, progresso percebido, algo que incomodou
4. Mensal: dashboard passivo + 1 NPS

### Flow 4 — Educador escola (futuro Mês 6+)
1. Conta escola (separada de família)
2. Cadastra alunos com perfis pedagógicos
3. Acompanha uso individual e coletivo
4. Plano de aula sugerido com cenas recomendadas
5. Exporta relatório PDF pra reunião com família

## Features

### In-scope Phase 1 (Demo Centelha, próximos 14 dias)
- 1 personagem (Téo Boto Rosa) totalmente animado em Lottie
- 1 música autoral em carimbó suave (gravação do músico amigo)
- 3 mini-jogos Nível 1 (toc-toc floresta) + 1 Nível 2 (sequência)
- PWA navegável (não precisa app nativo ainda pra demo)
- Onboarding básico
- Mockup Figma das telas futuras

### In-scope Phase 2 (12 meses pós-aprovação Centelha)
- 4 personagens completos (Téo, Lulu, Tião, Cuca)
- 12 músicas autorais (3 por personagem) em 4 ritmos regionais
- 5 PRESETS adaptativos funcionais
- Onboarding 12 perguntas + mapeamento perfil
- 5 níveis de progressão (causa-efeito → sequência → música completa → composição → canto)
- Dashboard pais + dashboard educadores
- IA rules engine on-device + telemetria agregada opt-in
- Beta com 30 famílias + piloto 3 escolas
- App publicado iOS + Android + PWA web

### Out of scope Phase 1-2
- ❌ Vídeo passivo longo (anti-Cocomelon)
- ❌ Câmera / mic passivo / biométrico
- ❌ Cross-app tracking / ad-tech / analytics third-party
- ❌ Loot boxes, rewards aleatórios, scroll infinito
- ❌ Push notifications >2×/sem
- ❌ Premium freemium Day 1 (deixa pra ano 2)
- ❌ Multi-idioma (PT-BR only Day 1)
- ❌ Expansão pra outras condições (autismo + TDAH + SD + apraxia + PC + DI suficiente)
- ❌ Editor de música pra pais (overhead técnico Day 1)
- ❌ Social features (não relevante criança especial)

## Success criteria

### 18 dias até deadline (11/jun/2026)
- ✅ Demo PWA com Téo Boto + carimbó + 3 jogos rodando
- ✅ Vídeo pitch 3min gravado e editado
- ✅ Pitch deck PDF 10 slides
- ✅ Mockups Figma 4 telas-chave
- ✅ 17 perguntas Fase 1 respondidas com rigor + screenshots demo + citações científicas
- ✅ Submissão DIA 7 ou 8/jun (NÃO 11)

### 12 meses pós-aprovação Centelha
- 4 personagens + 12 músicas + 5 PRESETS funcionais
- Beta 30 famílias com NPS ≥7
- Piloto 3 escolas Vigia
- 1.500 downloads
- 1 contrato B2G assinado
- Relatório técnico final FAPESPA aprovado

### Year 2-3
- 10k+ downloads
- 50+ escolas usando (SEDUC + APAE + privadas)
- R$275k/ano receita (3 streams)
- Captação Série Pré-Seed ou FINEP Tecnova
- Reconhecimento institucional (prêmios, mídia inclusiva BR)

## Contexto local Vigia/PA

Crítico saber:
- Vigia tem **~275 crianças com TEA + 950 com TDAH + 70 SD** = TAM local ~1.300+
- TAM nacional: **2,4M brasileiros com TEA** (IBGE 2022)
- INEP 2024: **918k matrículas TEA** (+44,4% vs 2023)
- Vigia tem 5G + Círio mais antigo PA (setembro = janela visibilidade)
- Sub-atendido por musicoterapeutas profissionais
- Acesso desigual a apps de inclusão (importados USD, sem identidade BR)

## Gap competitivo (= moat)

Nenhum app existente combina:
- Música ORIGINAL composta clinicamente (musicoterapeuta + músico)
- Personagens fauna **amazônica/brasileira**
- **Voz humana adaptável** por perfil sensorial (cantora do time)
- Ritmos **regionais BR** (carimbó, marambiré, lundu adaptados)
- **PT-BR nativo**
- **Multi-condição** com mesmo conteúdo modulado em 5 presets

Concorrentes (Otsimo TR/EN, Speech Blubs EN, Jade BR generalista, Tobii Dynavox AAC, Khan Kids genérico) NÃO cobrem esse nicho.
