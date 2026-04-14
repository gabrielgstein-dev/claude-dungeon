# Brainstorm: Dungeon como mapa vivo do repositório

## Problema com os projetos existentes

Todos os projetos de visualização de agentes AI (Pixel Agents, claude-office, agent-office, etc.)
fazem a mesma coisa: mostram o **agente fazendo trabalho**. É uma vitrine — você vê o personagem
animando enquanto o Claude digita. Nenhum mostra o **trabalho em si** como um mundo vivo.

## A oportunidade

A metáfora de dungeon é mais rica do que escritório porque tem progressão, quests e bosses.
Isso mapeia naturalmente para desenvolvimento de software:

| Dungeon              | Código real                                   |
|----------------------|-----------------------------------------------|
| Sala                 | Módulo / serviço / pasta do projeto           |
| Boss dentro da sala  | Issue aberta relacionada àquele módulo        |
| Loot                 | Feature entregue / PR mergeado                |
| Herói explorando     | Claude editando arquivos naquele módulo       |
| Sala "limpa"         | Sem issues abertas / testes passando          |
| Sala "corrompida"    | CI falhando / testes quebrando                |
| Nova sprint          | Novo andar do dungeon                         |

> Não existe sala de boss separada. O boss vive na sala onde o problema está.
> O herói vai até a sala porque está editando código ali — e encontra o boss ao chegar.

## Ideia central

O mapa do dungeon reflete o **mapa real do repositório**.

- Quando o Claude edita `auth/login.ts`, o herói caminha para a sala "Auth"
- Quando um PR é mergeado, a sala muda visualmente (torches acesas, boss derrotado)
- Issues abertas no GitHub aparecem como bosses/inimigos nas salas correspondentes
- Testes falhando deixam a sala "sombria" / corrompida

Isso transforma o projeto de "legal de ver" para **realmente útil**: você entende onde os
agentes estão trabalhando no código sem precisar abrir o terminal ou o GitHub.

## Diferencial competitivo

Todos os concorrentes mostram *quem* está trabalhando e *como*.
Este projeto mostraria *onde* no código e *qual o impacto* — o repositório como um mundo
que evolui conforme o trabalho acontece.

## O que precisaria ser construído

### 1. Integração com Claude Code hooks
- Ler eventos do Claude Code (arquivo editado, ferramenta usada, agente iniciado/finalizado)
- Mapear eventos para movimentação de heróis no dungeon
- A infraestrutura de WebSocket já existe no projeto

### 2. Mapeamento repo → salas
- Analisar a estrutura de pastas do projeto e gerar salas automaticamente
- Ex: `src/auth/` → Sala "Auth", `src/api/` → Sala "API", `tests/` → Sala "Testes"
- Salas podem ser geradas dinamicamente conforme o projeto cresce

### 3. Integração com GitHub
- Issues abertas → bosses/inimigos nas salas correspondentes
- PRs em review → boss "parado", aguardando
- PRs mergeados → boss derrotado, sala limpa, loot dropado
- Falha em CI → sala fica sombria / corrompida visualmente

### 4. Estado persistente do dungeon
- O dungeon "evolui" ao longo do tempo junto com o projeto
- Histórico de quests completadas (git log como lore do jogo)
- Salas que ficam mais "elaboradas" conforme mais trabalho é feito nelas (hot spots do código)

## Fases sugeridas

**Fase 1 — MVP:** Claude Code hook → herói caminha para a sala do arquivo sendo editado  
**Fase 2 — GitHub:** Issues como bosses, PRs como quests  
**Fase 3 — Dungeon vivo:** Estado persistente, histórico, hot spots visuais  

## Referências do ecossistema

- [Pixel Agents](https://github.com/pablodelucca/pixel-agents) — VS Code extension, lê JSONL do Claude Code
- [claude-office](https://github.com/paulrobello/claude-office) — full-stack, mais elaborado
- [agent-office](https://github.com/harishkotra/agent-office) — agentes Ollama locais, auto-contratação
- [Generative Agents / Smallville](https://arxiv.org/abs/2304.03442) — origem acadêmica da estética
- [ai-town (a16z)](https://github.com/a16z-infra/ai-town) — framework genérico para cidades de agentes
