# Roadmap

Visão geral de todas as fases, funcionalidades e dependências do projeto.
Para detalhamento de tarefas, ver [`phases.md`](phases.md).
Para histórico de decisões, ver [`log.md`](log.md).

---

## Visão geral

```
Fase 1          Fase 2          Fase 3          Fase 4
MVP         →   Quests      →   Dungeon Vivo →  Extensibilidade
(herói se       (combate        (saúde do        (hooks, skills,
 move)           real)           codebase)        plugins)
```

---

## Fase 1 — MVP
> Herói entra pelo portal e caminha para a sala do arquivo sendo editado.

**Critério de aceite:** abrir um projeto com claude-dungeon rodando, editar um arquivo
com o Claude Code e ver o herói se mover para a sala correta no mapa.

### Infraestrutura base
- [ ] Canvas 2D renderer com salas dinâmicas (geradas do repo, não hardcoded)
- [ ] Zoom e pan
- [ ] BFS pathfinding entre salas
- [ ] WebSocket server (Node.js + Express)
- [ ] Watcher de JSONL (`~/.claude/projects/`) com `chokidar`
- [ ] CLI para apontar o projeto a monitorar (`claude-dungeon --watch ./meu-projeto`)

### Mapeamento repo → salas
- [ ] Scanner de estrutura de pastas do projeto
- [ ] Gerar salas: cada pasta de nível 1 vira uma sala
- [ ] Atribuir posições e corredores no mapa automaticamente
- [ ] Renderizar nome da sala (nome da pasta)

### Herói e sessão
- [ ] Portal de sessão: abre quando Claude Code inicia, fecha quando termina
- [ ] Herói entra pelo portal ao início da sessão
- [ ] Herói caminha para a sala do arquivo sendo editado (`tool_use` no JSONL)
- [ ] Herói sai pelo portal ao fim da sessão
- [ ] Sprite default (idle + walk)
- [ ] Suporte a sprite customizado via `.claude/pixel-agent.json`

---

## Fase 2 — Quests
> Tasks do dev viram quests. Steps viram minions. Progresso visível em tempo real.

**Critério de aceite:** dar uma task com 4 steps ao Claude, ver a quest e os 4 minions
aparecerem na sala, e ver cada minion cair conforme os steps são completados.

### Sistema de quests e minions
- [ ] Detectar TodoWrite nos JSONL (task principal + steps)
- [ ] Spawnar quest como indicador visual na sala
- [ ] Spawnar minions (um por step) na sala ao início da quest
- [ ] Animar minion como `in_progress` quando step está ativo
- [ ] Animar morte do minion quando step é `completed`
- [ ] Animação de quest completa quando todos os steps terminam
- [ ] Loot dropado ao completar quest

### Detector de "Claude travado?"
- [ ] Monitorar frequência de eventos no JSONL
- [ ] Sem eventos por 2 min → interrogação acima da cabeça do herói
- [ ] Sem eventos por 5 min → sala pisca em alarme
- [ ] Retomar atividade → volta ao estado normal

### Subagentes como party
- [ ] Detectar spawn de subagentes no JSONL
- [ ] Cada subagente aparece como herói secundário
- [ ] Subagentes partem do herói principal para salas diferentes
- [ ] Subagente finalizado → herói secundário sai pelo portal

---

## Fase 3 — Dungeon Vivo
> O dungeon reflete a saúde real do codebase. Salas mudam de estado. Boss emerge da dívida técnica.

**Critério de aceite:** abrir um projeto com baixa cobertura de testes e ver as salas
correspondentes em estado corrompido, com boss presente. Adicionar testes e ver o boss enfraquecer.

### Estados das salas
- [ ] Analisar métricas por módulo: cobertura de testes, complexidade, TODOs, data do último commit, CI
- [ ] Renderizar estado visual por sala: saudável, empoeirada, em chamas, corrompida, selada, tesouro
- [ ] Atualizar estado em tempo real quando código muda
- [ ] Dungeon envelhece: salas sem commits acumulam pó visualmente ao longo do tempo

### Boss (dívida técnica)
- [ ] Calcular "nível de dívida" por módulo (cobertura, complexidade, TODOs, FIXME)
- [ ] Spawnar boss com tamanho/força proporcional à dívida
- [ ] Boss enfraquece conforme métricas melhoram
- [ ] Boss desaparece quando módulo atinge saúde aceitável
- [ ] Sprite de boss com variações por nível (pequeno, médio, grande)

### Corredores como acoplamento
- [ ] Analisar imports/exports entre módulos
- [ ] Corredor largo = alto acoplamento
- [ ] Corredor fino = módulos independentes
- [ ] Corredor em labirinto = dependências circulares

### Inspeção de sala
- [ ] Clicar na sala abre painel de inspeção
- [ ] Mostrar métricas detalhadas (cobertura, TODOs, última edição, CI status)
- [ ] Mostrar por que a sala está naquele estado
- [ ] Mostrar o que precisa melhorar para mudar de estado
- [ ] Botão "Aceitar quest" → gera prompt pronto para colar no Claude Code

### Resumo de sessão
- [ ] Ao fechar sessão, exibir recap estilo dungeon run
- [ ] Mostrar: quests completadas, minions derrotados, salas que melhoraram/pioraram
- [ ] Mostrar hooks disparados durante a sessão
- [ ] Mostrar boss status (enfraqueceu? ainda ativo?)

---

## Fase 4 — Extensibilidade
> Hooks visíveis, skills e plugins. O dungeon vira uma plataforma.

**Critério de aceite:** ter um hook registrado no `.claude/settings.json`, executar uma
sessão com Claude Code e ver o mecanismo da sala se ativar quando o hook dispara.

### Hooks visíveis
- [ ] Ler hooks registrados no `.claude/settings.json` do projeto
- [ ] Mapear tipo de hook para mecanismo visual na sala
  - `PreToolUse` → runa acende antes do herói agir
  - `PostToolUse` → mecanismo dispara depois da ação
  - `Stop` → portal pulsa ao fechar sessão
  - `Notification` → sino ressoa na entrada da sala
- [ ] Mostrar no resumo de sessão quais hooks dispararam e onde

### Skills (ações da UI)
- [ ] Rodar testes da sala selecionada
- [ ] Gerar prompt de limpeza para a sala selecionada
- [ ] Mostrar git blame da sala (quem tocou por último e quando)
- [ ] Exportar relatório do dungeon como markdown
- [ ] Suporte a skills customizadas via `.claude-dungeon/skills.json`

### Plugins
- [ ] API de plugin: fontes de dados externas para estado de sala e boss
- [ ] `plugin-github`: CI status, PRs e issues como eventos
- [ ] `plugin-sentry`: erros em produção como bosses adicionais
- [ ] `plugin-linear` / `plugin-jira`: issues externas como quests
- [ ] `plugin-sonarqube`: métricas de qualidade substituindo análise local
- [ ] Documentação da API para plugins da comunidade

---

## Dependências entre fases

```
Fase 1 (infraestrutura base)
  └── Fase 2 (precisa do watcher JSONL e do sistema de heróis)
        └── Fase 3 (precisa do sistema de salas e do herói se movendo)
              └── Fase 4 (precisa de tudo acima funcionando)
```

Cada fase entrega valor independente. Fase 1 já é utilizável sozinha.

---

## Fora de escopo (por enquanto)

- Editor de pixel art embutido (dev usa Aseprite/Piskel externamente)
- Multiplayer entre times (possível fase 5)
- Mobile / app nativo
- Suporte a outros agentes além do Claude Code
