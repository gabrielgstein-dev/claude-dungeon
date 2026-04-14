# Brainstorm: Dungeon como mapa vivo do repositório

## Problema com os projetos existentes

Todos os projetos de visualização de agentes AI (Pixel Agents, claude-office, agent-office, etc.)
fazem a mesma coisa: mostram o **agente fazendo trabalho**. É uma vitrine — você vê o personagem
animando enquanto o Claude digita. Nenhum mostra o **trabalho em si** como um mundo vivo.

## O problema real do dev

> "Não sei se o Claude está pensando ou se ele travou."

O dev larga o teclado, pega o celular, volta, ainda rodando — travou? O Claude Code é uma caixa
preta em execução. O dungeon resolve isso tornando o progresso **visível e legível** em tempo real.

---

## Sistema de camadas

### Camada 1 — Estado das salas (saúde do módulo)

Cada sala tem um estado visual derivado de métricas reais do módulo. Sem configuração — nasce
da análise do código.

| Estado        | Visual                        | Sinal do código                               |
|---------------|-------------------------------|-----------------------------------------------|
| Saudável      | Iluminada, ativa              | Testes verdes, commits recentes, CI ok        |
| Empoeirada    | Escura, teias de aranha       | Sem commits há meses, sem testes              |
| Em chamas     | Fogo, vermelho                | CI falhando, build quebrado                   |
| Corrompida    | Roxa, sombria                 | Alta complexidade ciclomática, muitos TODOs   |
| Selada        | Porta trancada                | Módulo deprecated, sem acesso                 |
| Tesouro       | Dourada, brilhante            | Feature recém entregue, cobertura alta        |

### Camada 2 — Boss (dívida técnica)

O boss não é criado manualmente. Ele **emerge** da análise estática do módulo:
- Baixa cobertura de testes → boss mais forte
- Muitos TODO/FIXME → boss maior
- Alta complexidade ciclomática → boss mais agressivo
- Sem commits há muito tempo → boss enferrujado mas presente

O boss **enfraquece** conforme a dívida é paga. Desaparece quando o módulo atinge saúde aceitável.

### Camada 3 — Quests e Minions (fluxo de trabalho)

Fonte de verdade: **TodoWrite do Claude Code** (JSONL em `~/.claude/projects/`).

| Elemento         | O que é                            | Fonte                          |
|------------------|------------------------------------|--------------------------------|
| Quest            | Task que o dev deu ao Claude       | Item principal do TodoWrite    |
| Minion           | Step da task                       | Sub-item do TodoWrite          |
| Minion lutando   | Step `in_progress`                 | Status do TodoWrite            |
| Minion morre     | Step `completed`                   | Status update no JSONL         |
| Quest completa   | Todos os steps `completed`         | Todos os todos `completed`     |
| Loot             | PR mergeado / feature entregue     | GitHub webhook                 |

### Camada 4 — Hooks como mecanismos da sala

O dungeon lê os hooks que o dev **já registrou** no `.claude/settings.json`. Nenhuma
configuração extra. Quando o Claude dispara um hook, o mecanismo da sala se ativa visualmente.

| Hook Claude Code | Visual no dungeon                              |
|------------------|------------------------------------------------|
| `PreToolUse`     | Runa acende antes do herói agir                |
| `PostToolUse`    | Mecanismo dispara depois da ação               |
| `Stop`           | Portal pulsa quando a sessão fecha             |
| `Notification`   | Sino ressoa na entrada da sala                 |

Muitos devs esquecem quais hooks têm registrados. O dungeon torna visível o que já existe —
em qual sala, em qual momento, disparado por qual ação.

---

## Mecânicas especiais

### Subagentes como party

Quando o Claude Code spawna subagentes para uma task complexa, cada subagente aparece como
um herói diferente que parte do principal e vai para uma sala diferente simultaneamente.
Você vê o "time" se dividindo pelo mapa — que é exatamente o que está acontecendo no código.

### Corredores como acoplamento

A largura e o estado dos corredores entre salas refletem o **acoplamento entre módulos**:
- Muitos imports/exports entre dois módulos → corredor largo e movimentado
- Módulos independentes → corredor fino e quieto
- Dependências circulares → corredor em labirinto, bloqueado

Você está visualizando a arquitetura do projeto, não só o trabalho em andamento.

### Portais de sessão

Quando o Claude Code abre uma sessão, um **portal aparece na entrada do dungeon** e o herói
entra. Quando a sessão fecha, o herói caminha de volta ao portal e some. O ciclo de vida das
sessões vira narrativa — sem o herói simplesmente desaparecer do mapa.

### Dungeon envelhece entre as tasks

Quando ninguém está trabalhando, o dungeon não está morto — ele **envelhece**:
- Salas sem commit acumulam pó visualmente com o tempo
- O boss de dívida técnica cresce devagar
- Uma sala sem toque há 6 meses parece diferente de uma tocada ontem

### "Claude está pensando ou travado?"

O herói tem estados visuais baseados nos eventos do JSONL:

| Evento JSONL           | Visual do herói                         |
|------------------------|-----------------------------------------|
| `tool_use` chegando    | Herói em ação (ataque, leitura, escrita)|
| Sem eventos por 2 min  | Interrogação acima da cabeça            |
| Sem eventos por 5 min  | Sala pisca em alarme                    |

---

## Inspeção de sala

Clicar em uma sala abre um painel com o **porquê** e o **como resolver**:

```
Sala: auth/          [Em chamas 🔥]

Por que está assim:
  ✗ CI falhando há 2 dias
  ✗ Cobertura de testes: 23% (mínimo: 70%)
  ✗ 8 TODO comments sem resolver

Como limpar:
  → Adicione testes até atingir 70% de cobertura
  → Resolva os TODOs ou converta em issues
  → Corrija o build quebrado

[ Aceitar quest → gera prompt pronto para o Claude ]
```

O botão "Aceitar quest" gera automaticamente o prompt com os passos específicos para
resolver aquela dívida técnica — pronto para colar no Claude Code.

---

## Resumo de sessão

Ao final de cada sessão, um recap estilo dungeon run:

```
Sessão de hoje — 1h23min
  ✓ 3 quests completadas
  ✓ 8 minions derrotados
  ↑ sala "auth" melhorou: corrompida → iluminada
  ⚡ 4 hooks disparados (2x pre-tool, 2x post-tool)
  ⚠ boss "api" ainda ativo (dívida técnica pendente)
  ✗ sala "tests/" continua em chamas
```

---

## Extensibilidade

### Skills (feitiços da UI)

Ações que o dev pode disparar diretamente do dungeon, sem sair para o terminal:

- "Rodar testes desta sala" → hero lança feitiço de cura, cobertura atualiza
- "Gerar prompt de limpeza" → prompt pronto para colar no Claude Code
- "Mostrar git blame" → quem tocou aqui por último e quando
- "Exportar relatório" → estado do dungeon como markdown

Devs podem adicionar skills customizadas via `.claude-dungeon/skills.json`.

### Plugins (fontes de dados externas)

O core do dungeon só lê Claude Code + métricas do código. Integrações extras são plugins:

- `plugin-github` → CI, PRs e issues como eventos
- `plugin-sentry` → erros em produção como bosses adicionais
- `plugin-linear` / `plugin-jira` → issues de qualquer ferramenta como quests
- `plugin-sonarqube` → métricas de qualidade substituindo análise estática local

API pública para a comunidade criar plugins. O dungeon fica lean — quem precisa de Jira instala.

---

## O ciclo completo

```
Sala corrompida → dev clica → entende por quê
  → aceita quest sugerida → cola prompt no Claude
  → herói entra pelo portal de sessão
  → quest spawna minions na sala
  → Claude executa steps → minions caem
  → hooks disparam → mecanismos acendem
  → subagentes partem para salas vizinhas
  → quest completa → loot dropado
  → boss enfraquece → sala muda de estado
  → sessão fecha → herói sai pelo portal
  → resumo de sessão aparece
```

---

## Por que é diferente de tudo que existe

| Concorrentes                      | claude-dungeon                                     |
|-----------------------------------|----------------------------------------------------|
| Mostram *quem* trabalha           | Mostra *onde* no código e *qual o impacto*         |
| Vitrine de atividade do agente    | Espelho da saúde do codebase                       |
| Mapa fixo (escritório genérico)   | Mapa gerado do repositório real                    |
| Boss = issue manual               | Boss emerge da dívida técnica automaticamente      |
| Sem progressão narrativa          | Quests, minions, loot — progressão real            |
| Requer configuração               | Zero config — lê o fluxo natural do Claude Code    |
| Hooks invisíveis                  | Hooks visualizados como mecanismos da sala         |
| Um agente por vez                 | Subagentes como party no mapa                      |
| Arquitetura invisível             | Corredores como acoplamento entre módulos          |

---

## Fases sugeridas

**Fase 1 — MVP:** Claude Code hook → herói entra pelo portal → caminha para a sala do arquivo  
**Fase 2 — Quests:** TodoWrite → quests + minions em tempo real + "Claude travado?" detector  
**Fase 3 — Dungeon vivo:** Métricas → estado das salas + boss + inspeção + resumo de sessão  
**Fase 4 — Extensibilidade:** Hooks visíveis + skills + plugins + GitHub  

---

## Referências do ecossistema

- [Pixel Agents](https://github.com/pablodelucca/pixel-agents) — VS Code extension, lê JSONL do Claude Code
- [claude-office](https://github.com/paulrobello/claude-office) — full-stack, mais elaborado
- [agent-office](https://github.com/harishkotra/agent-office) — agentes Ollama locais, auto-contratação
- [Generative Agents / Smallville](https://arxiv.org/abs/2304.03442) — origem acadêmica da estética
- [ai-town (a16z)](https://github.com/a16z-infra/ai-town) — framework genérico para cidades de agentes
