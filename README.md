# elamazonia-kids

Plataforma musical interativa para **crianças com necessidades especiais** (TEA, TDAH, Síndrome de Down, apraxia, paralisia cerebral, deficiência intelectual).

Personagens da fauna amazônica cantando músicas autorais inspiradas em ritmos paraenses (carimbó, brega marajoara). IA adaptativa aprende como cada criança interage e personaliza progressão por tipo de necessidade.

## Visão

Universo musical interativo onde crianças com necessidades especiais brincam com personagens da Amazônia que cantam, tocam e ensinam — e a IA aprende com cada criança pra adaptar o que vem depois.

**Progressão de complexidade:**
1. **Toc-toc Floresta** — causa-efeito puro (toca = som + animação)
2. **Sequência simples** — acompanhe a melodia
3. **Música completa** — vários personagens, ritmo
4. **Composição** — crie sua própria sequência
5. **Canto** — karaokê adaptado (esforço > precisão)

## Time

- **Gabriel** — técnico, dev, gestão
- **Músico amigo** — composição + gravação (formado teclado/piano, professor 5 instrumentos)
- **Cantora amiga** — vozes adaptáveis por tipo de necessidade

## Stack

- **Mobile principal:** Expo SDK 55 + Expo Router + NativeWind v4
- **Web (admin pais + landing):** Next.js 16 + App Router + Tailwind v4
- **Backend:** tRPC v11 (shared web + mobile)
- **DB:** Postgres 18 (Neon) + Drizzle 1.0
- **Auth:** Better Auth (family-friendly)
- **Audio:** expo-av + Howler.js
- **Animações:** Lottie + Rive
- **IA adaptativa:** Claude/Gemini API + rules-based Day 1 → ML Year 2
- **Monorepo:** Turborepo + pnpm 11

## Status

Day 1 — foundation. Sem features ainda. Veja [context/progress-tracker.md](context/progress-tracker.md).

## Documentação

- **Pra agentes IA:** [AGENTS.md](AGENTS.md) — leia primeiro
- **Contexto do projeto:** [context/](context/)
- **Decisões arquiteturais:** [context/decisions/](context/decisions/)

## Desenvolvimento

Requisitos: Node 22+, pnpm 11+

```bash
pnpm install
pnpm dev
```

## Licença

A definir.

## Contexto institucional

Projeto candidato ao **FAPESPA Centelha 3** (deadline 11/jun/2026). Origem: Vigia/PA. Foco social: inclusão de crianças neurodivergentes do Norte com acesso desigual a music therapy profissional.
