# Controle de Bordo

Registro cronológico de decisões, descobertas e progresso do projeto.

---

## 2026-04-13

### Origem do projeto

Partimos de uma análise do projeto [`thousandsky2024/claude-pixel-agent-web`](https://github.com/thousandsky2024/claude-pixel-agent-web) — um dungeon pixel art onde agentes Claude aparecem como heróis. O projeto foi feito via Manus e estava com pouca manutenção ativa.

Contribuímos com zoom/pan no renderer Phaser (`DungeonMapPhaser.tsx`) e abrimos [PR #1](https://github.com/thousandsky2024/claude-pixel-agent-web/pull/1) para o repo original.

### Análise do ecossistema

Pesquisamos projetos similares e encontramos um espaço movimentado surgido em 2025:

- **[Pixel Agents](https://github.com/pablodelucca/pixel-agents)** — extensão VS Code, viral no Fast Company. Escritório pixel art que lê JSONL do Claude Code.
- **[claude-office](https://github.com/paulrobello/claude-office)** — full-stack (Next.js + PixiJS). Boss = agente principal, employees = subagentes.
- **[agent-office](https://github.com/harishkotra/agent-office)** — agentes Ollama locais, auto-contratação.
- **[Generative Agents / Smallville](https://arxiv.org/abs/2304.03442)** — origem acadêmica da estética (Stanford/Google, 2023).

**Conclusão:** todos os projetos de destaque usam tema de **escritório**. O tema dungeon/fantasia é menos concorrido e mais rico em metáforas de progressão.

### Decisão de direção

**Insight central:** os projetos existentes mostram o agente fazendo trabalho. Nenhum mostra o trabalho em si como um mundo vivo.

**Nossa aposta:** o mapa do dungeon reflete o repositório. Salas = módulos. Bosses = issues. PRs mergeados = salas limpas. O dungeon evolui com o projeto.

Documentado em [`docs/brainstorm.md`](brainstorm.md).

### Decisões técnicas

| Decisão | Escolha | Alternativa descartada | Motivo |
|---|---|---|---|
| Rendering | Canvas 2D puro | Phaser 3 | Phaser é ~1MB+, overkill para um dev tool |
| Real-time | WebSocket nativo | tRPC | Menos overhead para stream de eventos |
| Backend | Express + REST | tRPC | Simplicidade |
| Sprites customizados | Spritesheet + `pixel-agent.json` | Editor embutido | Menos complexidade, dev usa a ferramenta que quiser |

### Estrutura de sprites

Definido o formato de spritesheet em [`docs/sprite-spec.md`](sprite-spec.md):
- Frame base: 16×16 px
- Rows por animação: idle, walk right, walk left, talk
- Config via `.claude/pixel-agent.json` no projeto do dev
- Fallback para sprite default se não houver config

### Repo criado

- **URL:** https://github.com/gabrielgstein-dev/claude-dungeon
- **Visibilidade:** público
- **Branch principal:** `main`

---

## Próximos passos

- [ ] Começar implementação da Fase 1 (ver [`docs/phases.md`](phases.md))
- [ ] Escolher sprites default para o projeto
- [ ] Prototipar o gerador de salas a partir de estrutura de pastas
