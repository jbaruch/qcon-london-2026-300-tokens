# The Right 300 Tokens Beat 100k Noisy Ones

**The Architecture of Context Engineering**

QCon London 2026 | Baruch Sadogursky & Patrick Debois

## What's Here

### Demo Recordings (`demo/`)

Asciinema recordings of all six live demos from the talk. Play with:

```bash
brew install asciinema
asciinema play demo/demo-01-stuffed-wrong.cast
```

| Recording | Antipattern | What Happens |
|-----------|-------------|-------------|
| `demo-01-stuffed-wrong` | Stuffed Prompt | 700-line CLAUDE.md with conflicting conventions. Agent follows the wrong ones. Tests fail. |
| `demo-02-stuffed-right` | Stuffed Prompt | Focused 135-line skill replaces the bloated CLAUDE.md. Agent follows correct conventions. Tests pass. |
| `demo-03-wrongtool-wrong` | Wrong Tool | RAG search returns pidge v2 docs (higher similarity score). Agent implements v2 API. ImportError — v3 is installed. |
| `demo-04-wrongtool-right` | Wrong Tool | Pidge tessl tile provides exact v3 API reference. Agent implements correct async API. Tests pass. |
| `demo-05-goldfish-wrong` | Goldfish Agent | Agree on pidge v3. Clear context. Ask what we decided. Agent has no idea. |
| `demo-06-goldfish-right` | Goldfish Agent | Same conversation, but skill includes memory bank. Decision persisted to disk. After clear, agent reads it back. |

### Order Service Skill (`.claude/skills/order-service/`)

A Claude Code skill defining coding conventions for the demo project — error handling patterns, async practices, logging standards, response formats, testing workflows, and a memory bank for architectural decisions.

### Pidge Tile (`pidge-tile/`)

A complete tessl tile for the pidge v3 notification library:
- **Docs** — Full API reference (NotificationClient, Message, DispatchResult)
- **Rules** — Always-on constraints (use v3 async API, not v2)
- **Skill** — Integration best practices
- **Evals** — Auto-generated test scenarios

Install: `tessl install jbaruch/pidge`

Browse on registry: [tessl.io/registry/jbaruch/pidge](https://tessl.io/registry/jbaruch/pidge)

## The Four Antipatterns

| Antipattern | Fix |
|-------------|-----|
| **Stuffed Prompt** | Skills. Lazy-load context on demand. Treat them as context artifacts. |
| **Wrong Tool** | Four context channels. Context artifacts bundle skills + rules + docs. |
| **Goldfish Agent** | Don't trust built-in memory. Write decisions to disk. You control what survives. |
| **Vibes Eval** | LLM generates rubrics. You review. LLM judges. You assess the delta. |

## Links

- [Shownotes](https://speaking.jbaru.ch/qcon-london-2026-300-tokens) — slides, all links
- [Tessl](https://tessl.io) — context artifact registry
- [Agent Skills Standard](https://agentskills.org)
