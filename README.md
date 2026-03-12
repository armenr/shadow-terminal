# Shadow Terminal

**A two-terminal operating pattern for building complex software with AI agents. One agent plans. One agent executes. The human bridges.**

---

## The Problem

AI coding tools hit a ceiling on complex projects. Context fills up, decisions are forgotten, compaction wipes state. Single-agent workflows can't maintain strategic coherence across dozens of interconnected decisions — the agent that chose your database schema last Tuesday has no idea it made that choice today. The fix isn't a better agent. It's a better *organization*.

## The Pattern

```
┌─────────────────────┐         ┌─────────────────────┐
│   TERMINAL 1        │         │   TERMINAL 2        │
│   The Strategist    │         │   The Marshal        │
│                     │  prompt │                     │
│   Reads state       ├────────►│   Receives prompts  │
│   Crafts prompts    │         │   Decomposes tasks   │
│   Maintains strategy│◄────────┤   Dispatches agents  │
│   Never touches code│ debrief │   Synthesizes results│
│                     │         │   Maintains state    │
└─────────────────────┘         └─────────────────────┘
                    ▲               ▲
                    │    THE HUMAN  │
                    └───────┬───────┘
                            │
                  Carries prompts forward.
                  Carries debriefs back.
                  Makes all strategic decisions.
                  The only entity with the full picture.
```

- **Terminal 1 — The Strategist** (`meta/`): Reads the marshal's state files, crafts self-contained prompts, maintains strategic notes. Never touches code. Never talks to the marshal directly.
- **Terminal 2 — The Marshal** (`project/`): Receives prompts from the human, decomposes them into agent missions, dispatches specialists, synthesizes results. Maintains all state on disk. Doesn't know the strategist exists. A marshal commands forces — the specialists are the ones who fight.
- **The Human**: The bridge. The only one who sees both sides.

## Quick Start

```bash
git clone https://github.com/armenr/shadow-terminal.git
cd shadow-terminal
```

**Terminal 1** — the strategist:
```bash
cd meta/
claude  # or your AI coding tool of choice
# Load the strategist with its CLAUDE.md instructions
# Tell it what you want to build
```

**Terminal 2** — the marshal:
```bash
cd project/
claude  # or your AI coding tool of choice
# Load the marshal with its CLAUDE.md instructions
# Paste the strategist's prompt here
```

**The cycle:**
1. Tell the strategist what you want to build
2. Copy the strategist's code-fenced prompt
3. Paste it into the marshal terminal
4. Let the marshal execute
5. Report results back to the strategist
6. Repeat

That's it. No framework to install. No dependencies. Just two terminals and a pattern.

## Repo Structure

```
shadow-terminal/
├── README.md                  # You are here
├── meta/                      # Strategist's workspace
│   ├── CLAUDE.md              # Strategist system prompt — strategy & prompt crafting
│   └── steering/              # Strategist's persistent state
│       ├── meta-notes.md      # Strategic observations (reverse-chronological)
│       └── prompt-log.md      # Prompt outcomes log (append-only)
├── project/                   # Marshal's workspace
│   ├── CLAUDE.md              # Marshal system prompt — field command & orchestration
│   └── .state/                # Marshal's persistent state
│       ├── ledger.md          # Master progress tracker
│       ├── decisions.md       # Decision log with rationale
│       ├── execution-plan.md  # Current plan with phases and deps
│       ├── agents/            # Agent output files
│       └── phases/            # Phase-level deliverables and outputs
└── examples/
    └── first-cycle.md         # Worked example of a complete dispatch cycle
```

## Key Concepts

### Compaction-Survivable State

Everything important lives on disk, not in context. When (not if) the AI's context window gets compacted or a session restarts, both agents reconstruct their awareness from state files. Context is ephemeral. Disk is durable.

### Self-Contained Prompts

The marshal has **zero memory** between prompts. Every prompt the strategist crafts must stand alone — purpose, end state, tasks, success criteria. State files bridge sessions, not conversation history.

### Dispatch Discipline

Every prompt explicitly names **who executes**. The marshal is a field commander — it decomposes and delegates. It never implements inline. The prompt says "dispatch a specialist agent" or "spawn a team of N agents," never "write the code yourself."

### Separation of Concerns

Strategic context (why are we building this? what's the architectural vision? what trade-offs did we accept?) stays in `meta/`. Operational context (what files changed? what tests pass? what's the current implementation state?) stays in `project/`. They never mix. This is what lets each agent stay focused and effective within its context budget.

### The OODA Loop

The marshal's core operating protocol: **Observe** (read state files, understand current position) → **Orient** (assess the prompt against project state) → **Decide** (plan decomposition and dispatch strategy) → **Act** (dispatch agents, synthesize results, update state). Every prompt triggers a full loop.

### Synthesis Tiers

Not all results deserve the same scrutiny:

- **Tier 1 — Verify**: Did the deliverables appear? Do basic checks pass? (Default for straightforward tasks.)
- **Tier 2 — Analyze**: Cross-reference outputs against requirements. Check for consistency, gaps, regressions.
- **Tier 3 — Adversarial Review**: Spawn a dedicated review agent to challenge the implementation. Use for critical paths, security-sensitive code, or architectural decisions.

### Commander's Intent

The strategist specifies **what** and **why**. The marshal decides **how**. "Build a REST API for user management with JWT auth" — not "create a file called routes.py with a Flask Blueprint." This lets the marshal leverage its operational expertise while the strategist maintains strategic direction.

### Compaction Recovery Protocol

Both agents have a recovery procedure: read their state files, reconstruct situational awareness, continue. The strategist reads `steering/meta-notes.md` and `steering/prompt-log.md`. The marshal reads `.state/ledger.md` and `.state/decisions.md`. Neither needs conversation history to function.

## When to Use This

- Projects spanning **more than a week** of work
- Multi-file, multi-module, multi-crate systems
- Anything where **losing strategic context would be costly**
- Projects with interconnected architectural decisions
- Codebases where a wrong early decision cascades into expensive rework

## When NOT to Use This

- Simple scripts and one-file projects
- Quick bug fixes
- Prototypes you'll throw away tomorrow
- Anything you can hold in your head

The overhead of two terminals and a dispatch cycle isn't worth it for small stuff. Use a single agent and move fast.

## Adapting the Pattern

The `CLAUDE.md` files in `meta/` and `project/` are templates. Adapt them to your project:

- **Strategist**: Adjust the state file paths, add domain-specific strategic concerns, tune the prompt format to your marshal's capabilities.
- **Marshal**: Define your project's state schema, set dispatch rules for your agent tooling, add domain-specific execution protocols.

The pattern works with any AI coding tool that supports system prompts and multi-agent dispatch. Claude Code, Cursor, Aider, Codex — the terminals don't care.

---

## Credits

Developed by [Armen Rostamian](https://rmnr.net). Read the full story: **[Stop Talking to Your AI](https://rmnr.net/blog/stop-talking-to-your-ai/)**.

## License

MIT
