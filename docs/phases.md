# Phases

## Phase 1 — MVP: Herói segue o agente

**Objetivo:** quando o Claude Code edita um arquivo, o herói caminha até a sala correspondente no dungeon.

**Critério de aceite:** abrir um projeto com `claude-dungeon` rodando, editar um arquivo com o Claude Code e ver o herói se mover para a sala certa no mapa.

### Tarefas

#### 1.1 Watcher de hooks do Claude Code
- [ ] Identificar onde o Claude Code grava os arquivos JSONL (`~/.claude/projects/`)
- [ ] Implementar watcher de arquivo com `chokidar` ou `fs.watch`
- [ ] Parsear eventos relevantes: `tool_use` (Read, Write, Edit, Bash)
- [ ] Extrair o caminho do arquivo sendo editado de cada evento

#### 1.2 Gerador de salas a partir do repo
- [ ] Analisar a estrutura de pastas do projeto alvo
- [ ] Gerar salas automaticamente: cada pasta de nível 1 vira uma sala
- [ ] Atribuir posições às salas no mapa (layout em grid ou orgânico)
- [ ] Definir corredores de conexão entre salas vizinhas

#### 1.3 Canvas 2D renderer
- [ ] Porta do `DungeonMap.tsx` do projeto de referência (Canvas 2D puro, sem Phaser)
- [ ] Adaptar para salas dinâmicas (não hardcoded)
- [ ] Zoom e pan (já resolvido no projeto de referência)
- [ ] Renderizar nome das salas (nome da pasta)

#### 1.4 Sistema de heróis
- [ ] Porta do BFS pathfinding do projeto de referência
- [ ] Hero state machine: idle → walking → arrived
- [ ] Carregar sprite default
- [ ] Carregar sprite customizado do `.claude/pixel-agent.json` se existir

#### 1.5 WebSocket server
- [ ] Servidor Express + WebSocket
- [ ] Evento `hero:move` disparado quando o watcher detecta mudança de arquivo
- [ ] Frontend subscreve e move o herói correspondente

#### 1.6 Config do projeto alvo
- [ ] CLI ou config file para apontar qual projeto monitorar
- [ ] Exemplo: `claude-dungeon --watch ~/projects/meu-projeto`

---

## Phase 2 — GitHub: Issues como bosses, PRs como quests

**Objetivo:** integrar com o GitHub para que issues abertas apareçam como bosses nas salas e PRs mergeados "limpem" as salas.

**Critério de aceite:** abrir uma issue no GitHub e ver o boss aparecer na sala correspondente. Fechar a issue e ver o boss morrer.

### Tarefas

#### 2.1 Integração GitHub
- [ ] Autenticação via GitHub App ou Personal Access Token
- [ ] Listar issues abertas do repositório monitorado
- [ ] Mapear issues para salas com base em labels ou path mencionado na issue
- [ ] Webhook para receber eventos em tempo real (issue opened/closed, PR merged)

#### 2.2 Bosses no mapa
- [ ] Boss sprite default por sala
- [ ] Boss aparece quando issue é aberta na sala
- [ ] Boss desaparece (animação de morte) quando issue é fechada
- [ ] Tooltip com título da issue ao passar o mouse no boss

#### 2.3 Quests / PRs
- [ ] PR aberto → quest aparece como indicador visual na sala
- [ ] PR mergeado → animação de "sala limpa" (torches acendem, cor muda)
- [ ] PR fechado sem merge → indicador some

#### 2.4 Painel lateral
- [ ] Lista de issues abertas (bosses ativos)
- [ ] Lista de PRs em aberto (quests ativas)
- [ ] Clicar em um item destaca a sala no mapa

---

## Phase 3 — Dungeon vivo: estado persistente e hot spots

**Objetivo:** o dungeon evolui com o tempo, refletindo a história do projeto. Salas ficam mais elaboradas onde há mais trabalho acumulado.

**Critério de aceite:** salas com mais commits/edições têm visual diferente das salas pouco tocadas. O histórico de quests completadas é visível como "lore".

### Tarefas

#### 3.1 Estado persistente
- [ ] Banco de dados leve (SQLite via Drizzle ou arquivo JSON)
- [ ] Persistir posição dos heróis entre sessões
- [ ] Persistir estado das salas (limpa, com boss, corrompida)
- [ ] Histórico de eventos (quem editou o quê e quando)

#### 3.2 Hot spots visuais
- [ ] Analisar `git log` para contar commits por pasta
- [ ] Salas com mais commits ficam mais "elaboradas" visualmente (mais decoração, iluminação)
- [ ] Salas há muito tempo sem edição ficam "empoeiradas" (efeito visual de abandono)

#### 3.3 Lore / histórico
- [ ] Painel de "lore" com git log formatado como história
- [ ] Commits aparecem como entradas de diário de aventureiro
- [ ] Milestones e releases como "capítulos" da história

#### 3.4 Multiplayer
- [ ] Múltiplos desenvolvedores do mesmo time aparecem como heróis diferentes
- [ ] Cada dev tem seu próprio sprite (via `.claude/pixel-agent.json`)
- [ ] Presença em tempo real: ver onde cada colega está trabalhando no código
