# State

<!-- Where we are right now. Top section is field-shaped for quick scan. Resume note is free-form prose — as long as it needs to be (planning lives here for multi-step work). Past sessions live in git. -->

Project:  LLM Council
Topic:    idle
Phase:    idle — rails installed, no piece started
Blocker:  none
Last:     2026-07-28 19:30 — td-flow initialized on the existing fork; docs written, hook installed

## Resume note

[mailbox] unavailable — GitHub Issues are disabled on this repo

Setup session only. No application code was touched — the entire diff is td-flow scaffolding plus the root `CLAUDE.md` swap.

**Where the project stands.** The working directory was empty; the project was cloned from `github.com/kzkvh2/llm-council`, a fork of `karpathy/llm-council`. The app is functional as inherited (3-stage council pipeline) but has never been run in this checkout. Active scope in `PROJECT.md` is deliberately empty — the user chose "just get set up" and has not picked a first piece.

**Before anything can run:**
- `uv sync` and `cd frontend && npm install` — neither has been run here, so the pre-commit hook's test command will fail on the next real commit
- `.env` with `OPENROUTER_API_KEY` — doesn't exist; `.env.example` documents it

**Decisions made this session** (already reflected in `WORKWAY.md`, listed here so they aren't re-litigated):
- Test command is `cd frontend && npm run lint && cd .. && uv run python -c 'import backend.main'` — chosen over lint-only specifically to catch the relative-import breakage that is this repo's most common failure
- UAT is mine to run, both backend curl and browser. OpenRouter credit spend is authorized; keep runs small (short prompt, trimmed `COUNCIL_MODELS`)

**Two constraints that bite td-flow itself:**
1. Default branch is `master`. Every td-flow command says `origin/main` — substitute.
2. **GitHub Issues are disabled on this repo**, so `/td-flow-park`, `/td-flow-mailbox`, `/td-flow-snapshot`, and `/td-flow-close`'s parking step will all fail. `BACKLOG.md` is the only parking place until the user enables issues in repo settings. Do not attempt to flush the backlog until then. This was surfaced to the user at clear time and is awaiting their call.

**Preserved content.** The repo's original root `CLAUDE.md` (167 lines) is verbatim at `.td-flow/frameworks/preserved-claude-md.md`; the operational parts are distilled into `WORKWAY.md § Framework specifics`. Its "Future Enhancement Ideas" list is a reasonable menu when picking the first piece.

**Unverified.** Model IDs in `backend/config.py` are pinned to Nov 2025 (`openai/gpt-5.1`, `google/gemini-3-pro-preview`, `anthropic/claude-sonnet-4.5`, `x-ai/grok-4`). Nothing has confirmed they still resolve on OpenRouter — check the catalog before treating a failed call as a code bug.
