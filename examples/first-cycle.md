# First Cycle: A Worked Example

A complete walkthrough of one dispatch cycle using the Shadow Terminal pattern.

**Scenario**: Build a Python CLI tool that manages a reading list — add books, list books, mark as read, persist to a JSON file.

---

## Step 1: Tell the Strategist What You Want

Open Terminal 1 (the strategist). Type naturally:

```
I want to build a Python CLI tool that manages a reading list.
Features: add a book, list all books, mark a book as read.
Persist everything to a local JSON file. Keep it simple — no database,
no server, just a CLI.
```

The strategist asks a couple of clarifying questions — "Any preference on CLI framework? argparse, click, typer?" You say: "argparse, no external deps." It might also ask about target Python version or testing expectations. Answer briefly and let it work.

## Step 2: The Strategist Crafts a Prompt

The strategist produces a code-fenced block. It looks something like this:

````
```PASTE THIS TO THE MARSHAL
PURPOSE: Build a Python CLI tool for managing a personal reading list.
This is the project's first deliverable — establishing the core data model,
CLI interface, and persistence layer.

TODAY'S DATE: 2026-03-12

END STATE:
- A working CLI (`reading_list.py` or equivalent entry point)
- Four commands: add, list, read (mark as read), remove
- JSON file persistence (~/.reading_list.json or configurable)
- Minimum 8 unit tests covering all commands and edge cases
- Clean error handling (missing file, duplicate title, book not found)

KEY TASKS:
1. Design the data model (title, author, status, date_added)
2. Implement CLI argument parsing with argparse (no external dependencies)
3. Implement JSON persistence with atomic writes
4. Write unit tests for all commands and error paths
5. Add a brief --help for each subcommand

DISPATCH: Dispatch a specialist agent with the following mission brief.
The agent should implement all code, write all tests, and verify tests pass
before reporting back. Do not implement this yourself — delegate to a
specialist.

SUCCESS CRITERIA:
- All 4 commands work correctly from the command line
- Tests pass with no failures
- JSON file is created/updated correctly
- Error messages are clear and actionable
- Code is clean, typed, and follows PEP 8

CONSTRAINTS:
- Python 3.10+ (use modern syntax)
- No external dependencies (stdlib only)
- Single-file is fine for v1 — don't over-architect
```
````

Notice what's in there and what isn't:
- **What and why** — yes. **How to structure the code** — no. That's the marshal's domain.
- **Success criteria** — explicit and verifiable.
- **Dispatch instruction** — the marshal must delegate, not implement inline.
- **Date** — temporal grounding so the marshal knows when it is.
- **No assumptions carried from conversation** — the prompt stands alone.

## Step 3: Paste to the Marshal

Switch to Terminal 2 (the marshal). Paste the code-fenced block verbatim. Nothing else — no extra commentary, no "hey, here's what I need." The prompt is the prompt.

## Step 4: The Marshal Decomposes and Dispatches

The marshal reads the prompt and executes its OODA loop:

1. **Observe**: Checks `.state/ledger.md` — this is a fresh project, no prior state.
2. **Orient**: First deliverable, straightforward scope, single-agent sufficient.
3. **Decide**: One specialist agent, no team needed. Creates an execution plan.
4. **Act**: Writes the plan to `.state/ledger.md`, creates a phase entry in `.state/phases/`, dispatches the agent.

The marshal writes something like this to its ledger:

```markdown
## Phase 1: Reading List CLI
- Status: IN PROGRESS
- Agent: specialist (Python CLI)
- Deliverables: reading_list.py, test_reading_list.py
- Success criteria: 4 commands, 8+ tests, JSON persistence
```

Then it dispatches the specialist with a self-contained brief — the specialist gets the mission, constraints, and success criteria. It does not get strategic context about the project's future. It doesn't need it.

## Step 5: The Specialist Implements

The specialist agent does what it does best — writes code:

- Creates `reading_list.py` with argparse subcommands (add, list, read, remove)
- Implements a `BookStore` class handling JSON serialization
- Adds typed dataclass for `Book` (title, author, status, date_added)
- Writes `test_reading_list.py` with tests for each command and edge cases
- Runs the tests, confirms they pass
- Reports back to the marshal with a summary of what was built

## Step 6: The Marshal Synthesizes

The marshal applies **Tier 1 synthesis** (verify) — appropriate for a straightforward first deliverable:

- Confirms both files exist
- Confirms tests pass (8/8)
- Checks that each success criterion from the prompt is met
- Notes one observation: JSON persistence uses simple `open()`/`json.dump()` — no file locking or atomic writes. Flags this for future hardening.

The marshal updates `.state/ledger.md`:

```markdown
## Phase 1: Reading List CLI
- Status: COMPLETE
- Deliverables: reading_list.py, test_reading_list.py
- Tests: 8/8 passing
- Note: JSON writes are not atomic — acceptable for v1, flag for hardening
```

Updates `.state/decisions.md`:

```markdown
## 2026-03-12 — Data model design
- Decision: Flat JSON array of book objects
- Rationale: Simplest persistence for v1, easy to migrate later
- Trade-off: No indexing, O(n) lookups — fine for personal reading lists
```

## Step 7: Debrief the Strategist

Switch back to Terminal 1. Report what happened:

```
It worked. Agent implemented all 4 commands plus a remove command.
8 tests passing. Marshal noted that JSON writes aren't atomic — flagged
as a future concern but acceptable for v1. Data model is a flat JSON
array with title, author, status, date_added. Marshal chose a dataclass
approach.
```

Keep it factual. The strategist doesn't need to see the code or test output — it needs to know: did it work, what decisions were made, what was flagged.

## Step 8: The Strategist Updates and Prepares

The strategist processes the debrief:

- Logs the prompt outcome to `steering/prompt-log.md`:

```markdown
## 2026-03-12 — Reading list CLI v1
- Intent: First deliverable, core CLI with persistence
- Structure: Prescriptive (first prompt, clear scope)
- Result: SUCCESS
- Note: Marshal correctly flagged atomic writes. Dispatch discipline held.
```

- Updates `steering/meta-notes.md` if strategically relevant (for a simple first cycle, it might just note "Project bootstrapped, clean first cycle").

- Ready for the next directive. You might say: "Next I want to add search and filtering." The strategist would craft the next prompt, referencing what it knows about the data model decision from the marshal's state files.

---

## Key Lessons

**The strategist never saw a line of Python.** It crafted a strategic prompt with clear success criteria and dispatch instructions. That's its entire job.

**The marshal never decided project strategy.** It received a self-contained prompt, decomposed it, dispatched a specialist, verified the results, and updated state. It doesn't know why a reading list tool exists or what's coming next.

**State survived the entire cycle on disk.** The ledger, decision log, strategic notes, and prompt log are all on the filesystem. Nothing critical lives only in a context window.

**If context compacted at any point, both agents could recover.** The strategist would read `steering/meta-notes.md` and `steering/prompt-log.md`. The marshal would read `.state/ledger.md` and `.state/decisions.md`. Both would reconstruct full situational awareness and continue.

**The human made exactly one strategic decision** (what to build, with what constraints) **and one quality judgment** (the debrief: "it worked, the atomic writes flag is fine for now"). Everything else was delegated. That's the point — the human stays at the strategic level, the agents handle the operational complexity.

---

## What Comes Next

The second cycle is where the pattern starts to shine. The strategist now has:
- A prompt log showing what worked
- Knowledge of the data model decision (from the marshal's state)
- A flagged concern (atomic writes) to address at the right time

The next prompt it crafts will build on this foundation — without the marshal needing to remember anything. The state files carry the continuity. The strategist carries the strategy. The human carries both forward.

See the [README](../README.md) for the full pattern reference.
