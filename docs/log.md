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

---

## 2026-04-13 (continuação) — Sistema de camadas definido

### Insight principal

O dev usa Claude Code com tasks que têm steps (via TodoWrite). Esses steps são o
fluxo natural de trabalho — e já estão gravados nos arquivos JSONL. Isso fecha o
sistema de combate sem nenhuma configuração extra.

### Sistema de 3 camadas

**Camada 1 — Estado das salas** (saúde do módulo, derivada de métricas do código):
- Iluminada → testes verdes, CI ok, commits recentes
- Empoeirada → sem commits há meses, sem testes
- Em chamas → CI falhando, build quebrado
- Corrompida → alta complexidade, muitos TODOs
- Tesouro → feature recém entregue, cobertura alta

**Camada 2 — Boss** (dívida técnica, emerge automaticamente):
- Não é criado manualmente — nasce da análise estática do módulo
- Tamanho/força proporcional ao nível de dívida
- Enfraquece conforme o código melhora
- Desaparece quando o módulo atinge saúde aceitável

**Camada 3 — Quests e Minions** (fluxo real do dev, fonte: TodoWrite):
- Task principal → Quest
- Cada step da task → Minion
- Step `in_progress` → Minion combatendo
- Step `completed` → Minion derrotado
- Quest completa → loot dropado

### Por que isso é diferente

Nenhum concorrente usa o TodoWrite do Claude Code como fonte de verdade do combate.
É o fluxo real do desenvolvedor virado em narrativa, sem configuração extra.

### Fases revisadas

1. MVP: hook → herói caminha para a sala do arquivo sendo editado
2. Quests: TodoWrite → quests + minions em tempo real
3. Dungeon vivo: métricas → estado das salas + boss de dívida técnica
4. GitHub: CI, PRs e loot

---

---

## 2026-04-13 (continuação) — Mecânicas, UX e extensibilidade

### Problema central definido

> "Não sei se o Claude está pensando ou se ele travou."

O dungeon resolve isso com estados visuais do herói baseados nos eventos do JSONL:
eventos chegando = herói em ação, silêncio por 2+ min = interrogação, 5+ min = alarme.

### Novas mecânicas adicionadas ao brainstorm

- **Inspeção de sala** — clicar na sala mostra por que está naquele estado e como limpar,
  com botão que gera o prompt pronto para colar no Claude Code
- **Resumo de sessão** — recap estilo dungeon run ao final de cada sessão
- **Subagentes como party** — cada subagente é um herói diferente que parte para uma sala
- **Corredores como acoplamento** — largura e estado refletem imports/exports entre módulos
- **Portais de sessão** — herói entra/sai por portal ao abrir/fechar sessão do Claude Code
- **Dungeon envelhece** — salas acumulam pó visualmente quando não há commits recentes

### Decisão sobre hooks

Não vamos pedir para o usuário configurar hooks no dungeon. Vamos **ler os hooks que ele
já registrou** no `.claude/settings.json` e visualizá-los como mecanismos da sala que
ativam quando o Claude os dispara. Zero configuração extra.

### Extensibilidade

- **Skills** — ações disparadas da UI (rodar testes, gerar prompt, git blame)
  com suporte a skills customizadas via `.claude-dungeon/skills.json`
- **Plugins** — fontes de dados externas (GitHub, Sentry, Linear, Jira, SonarQube)
  via API pública; core fica lean

### Fases revisadas

1. MVP: portal de sessão + herói caminha para a sala do arquivo
2. Quests: TodoWrite → quests + minions + detector de "travado"
3. Dungeon vivo: métricas → estado das salas + boss + inspeção + resumo
4. Extensibilidade: hooks visíveis + skills + plugins + GitHub

---

## Próximos passos

- [ ] Atualizar `phases.md` com todas as fases e mecânicas revisadas
- [ ] Começar implementação da Fase 1
- [ ] Escolher sprites default para o projeto
- [ ] Prototipar o gerador de salas a partir de estrutura de pastas
