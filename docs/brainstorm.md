# Brainstorm: Dungeon como mapa vivo do repositório

## Problema com os projetos existentes

Todos os projetos de visualização de agentes AI (Pixel Agents, claude-office, agent-office, etc.)
fazem a mesma coisa: mostram o **agente fazendo trabalho**. É uma vitrine — você vê o personagem
animando enquanto o Claude digita. Nenhum mostra o **trabalho em si** como um mundo vivo.

## A oportunidade

A metáfora de dungeon é mais rica do que escritório porque tem progressão, quests, minions e bosses.
Isso mapeia naturalmente para desenvolvimento de software:

| Dungeon              | Código real                                                  |
|----------------------|--------------------------------------------------------------|
| Sala                 | Módulo / serviço / pasta do projeto                          |
| Estado da sala       | Saúde do módulo (iluminada, empoeirada, em chamas, selada)   |
| Boss dentro da sala  | Dívida técnica acumulada naquele módulo                      |
| Quest                | Task que o dev deu ao Claude (TodoWrite)                     |
| Minions              | Steps da task (sub-itens do TodoWrite)                       |
| Herói lutando        | Claude executando cada step (`tool_use` no JSONL)            |
| Minion morre         | Step marcado como `completed`                                |
| Quest completa       | Todos os steps `completed`                                   |
| Loot                 | PR mergeado / feature entregue                               |
| Novo andar           | Nova sprint / milestone                                      |

> Não existe sala de boss separada. O boss vive na sala onde a dívida técnica está.
> O herói vai até a sala porque está editando código ali — e encontra o boss ao chegar.

## Ideia central

O mapa do dungeon reflete o **mapa real do repositório** e o **fluxo real de trabalho do dev**.

Quando você dá uma task ao Claude com steps:
```
Implement auth:
  1. Create user model
  2. Add login endpoint
  3. Add JWT middleware
  4. Write tests
```

Isso vira uma quest com 4 minions. O herói caminha até a sala "auth" e começa a lutar.
Cada step completado derruba um minion. Quando todos caem, a quest está completa.

Se a sala "auth" tem dívida técnica acumulada (baixa cobertura, muitos TODOs, código complexo),
há um boss esperando. A sala só fica verdadeiramente limpa quando a dívida é resolvida.

Isso transforma o projeto de "legal de ver" para **realmente útil**: você vê em tempo real
onde o Claude está, o que está fazendo e qual o estado de saúde de cada parte do código.

## Sistema de camadas

### Camada 1 — Estado das salas (saúde do código)
Cada sala tem um estado visual que reflete métricas reais do módulo:

| Estado        | Visual                         | Sinal do código                              |
|---------------|--------------------------------|----------------------------------------------|
| Saudável      | Iluminada, ativa               | Testes verdes, commits recentes, CI ok       |
| Empoeirada    | Escura, teias de aranha        | Sem commits há meses, sem testes             |
| Em chamas     | Fogo, vermelho                 | CI falhando, build quebrado                  |
| Corrompida    | Roxa, sombria                  | Alta complexidade ciclomática, muitos TODOs  |
| Selada        | Porta trancada                 | Módulo deprecated, sem acesso                |
| Tesouro       | Dourada, brilhante             | Feature recém entregue, cobertura alta       |

### Camada 2 — Boss (dívida técnica)
O boss não é criado manualmente. Ele **emerge** da qualidade do código:
- Baixa cobertura de testes → boss mais forte
- Muitos TODO/FIXME no código → boss maior
- Alta complexidade ciclomática → boss mais agressivo
- Sem commits há muito tempo → boss enferrujado mas presente

O boss **enfraquece** conforme a dívida é paga (testes adicionados, refatorações, TODOs resolvidos).
Desaparece quando o módulo atinge um nível saudável.

### Camada 3 — Quests e Minions (fluxo de trabalho)
Fonte de verdade: **TodoWrite do Claude Code** (arquivos JSONL em `~/.claude/projects/`).

- Task principal → Quest (aparece como indicador na sala)
- Cada step → Minion (spawna na sala quando a quest começa)
- Step `in_progress` → Minion combatendo o herói
- Step `completed` → Minion derrotado (animação de morte)
- Todos os steps `completed` → Quest completa, loot dropado

Isso funciona sem nenhuma configuração extra — é o fluxo natural do dev com Claude Code.

## Por que é diferente de tudo que existe

| Concorrentes                     | claude-dungeon                                    |
|----------------------------------|---------------------------------------------------|
| Mostram *quem* trabalha          | Mostra *onde* no código e *qual o impacto*        |
| Vitrine de atividade do agente   | Espelho da saúde do codebase                      |
| Mapa fixo (escritório genérico)  | Mapa gerado do repositório real                   |
| Bosses = issues manuais          | Boss emerge da dívida técnica automaticamente     |
| Sem progressão narrativa         | Quests, minions, loot — progressão real           |
| Requer configuração              | Zero config — lê o fluxo natural do Claude Code   |

## O que precisaria ser construído

### 1. Leitor de hooks do Claude Code
- Ler arquivos JSONL de `~/.claude/projects/`
- Extrair eventos: `tool_use` (arquivo editado), TodoWrite (tasks/steps), tool results
- Watcher em tempo real com `chokidar`

### 2. Mapeamento repo → salas
- Analisar estrutura de pastas do projeto monitorado
- Gerar salas: cada pasta de nível 1 vira uma sala
- Calcular métricas por sala: cobertura, complexidade, TODOs, data do último commit
- Atualizar estado visual da sala com base nas métricas

### 3. Sistema de boss (análise de dívida técnica)
- Escanear módulo por: arquivos sem testes, TODOs/FIXMEs, complexidade ciclomática
- Calcular "nível" do boss por sala
- Atualizar em tempo real quando código muda

### 4. Sistema de quests/minions (TodoWrite)
- Detectar criação de TodoWrite nos JSONL
- Spawnar quest + minions na sala correspondente
- Atualizar status dos minions conforme steps são completados

### 5. Renderer Canvas 2D
- Salas dinâmicas geradas do repo (não hardcoded)
- Estados visuais por sala
- Boss com tamanho variável por nível de dívida
- Minions por sala durante quests ativas
- Zoom e pan

### 6. Integração GitHub (fase posterior)
- PRs mergeados → loot na sala
- CI status → atualização do estado da sala em tempo real

## Fases sugeridas

**Fase 1 — MVP:** Claude Code hook → herói caminha para a sala do arquivo sendo editado
**Fase 2 — Quests:** TodoWrite → quests e minions aparecem na sala em tempo real
**Fase 3 — Dungeon vivo:** Métricas de código → estado das salas + boss de dívida técnica
**Fase 4 — GitHub:** CI, PRs e loot

## Referências do ecossistema

- [Pixel Agents](https://github.com/pablodelucca/pixel-agents) — VS Code extension, lê JSONL do Claude Code
- [claude-office](https://github.com/paulrobello/claude-office) — full-stack, mais elaborado
- [agent-office](https://github.com/harishkotra/agent-office) — agentes Ollama locais, auto-contratação
- [Generative Agents / Smallville](https://arxiv.org/abs/2304.03442) — origem acadêmica da estética
- [ai-town (a16z)](https://github.com/a16z-infra/ai-town) — framework genérico para cidades de agentes
