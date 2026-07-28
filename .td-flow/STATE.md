# State

<!-- Where we are right now. Top section is field-shaped for quick scan. Resume note is free-form prose — as long as it needs to be (planning lives here for multi-step work). Past sessions live in git. -->

Project:  LLM Council
Topic:    post-init setup fixes
Phase:    queued — agreed at clear time, nothing started
Blocker:  none in flight, but items 1–3 below block the first real code commit
Last:     2026-07-28 19:35 — td-flow initialized; four setup gaps queued for next session

## Resume note

[mailbox] unavailable — GitHub Issues are disabled on this repo

### START HERE — the user asked for these to be raised and fixed first

At the end of the init session the user said: *"bring these issues up next time we start and let's fix them."* Lead with this list before anything else. All four are authorized — don't re-ask whether to do them, just confirm the order and go.

1. **Enable GitHub Issues on the repo.** Forks default to off, and this blocks every td-flow command that files issues (`/td-flow-park`, `/td-flow-mailbox`, `/td-flow-snapshot`, `/td-flow-close`'s parking step). Until it's on, `BACKLOG.md` is the only parking place and must not be flushed. Fix: `gh repo edit kzkvh2/llm-council --enable-issues`. This changes public repo config, so say what you're doing as you do it.
2. **Install backend deps** — `uv sync` from the project root.
3. **Install frontend deps** — `cd frontend && npm install`. Until 2 and 3 are done the pre-commit hook fails on `eslint: not found` and every code commit is blocked. Verify the fix by running the test command directly: `cd frontend && npm run lint && cd .. && uv run python -c 'import backend.main'`.
4. **Create `.env` with `OPENROUTER_API_KEY`.** Doesn't exist; `.env.example` documents it. The key is the user's to supply — this is the one item that needs them, so ask for it rather than stalling on it. Nothing can query a model until it's set.

Once 1–4 are green: `./start.sh` and one short query end-to-end is the real proof the checkout works. That will spend a small amount of OpenRouter credit — already authorized (see UAT below).

### Context

Setup session only. No application code has been touched — the whole diff so far is td-flow scaffolding plus the root `CLAUDE.md` swap.

The working directory was empty; the project was cloned from `github.com/kzkvh2/llm-council`, a fork of `karpathy/llm-council`. The app is functional as inherited (3-stage council pipeline) but has never been run in this checkout. `PROJECT.md § Active scope` is deliberately empty — the user chose "just get set up" and hasn't picked a first piece. Once the four items above are done, that's the natural next conversation.

**Decisions already made** (reflected in `WORKWAY.md`; listed so they aren't re-litigated):
- Test command is `cd frontend && npm run lint && cd .. && uv run python -c 'import backend.main'` — chosen over lint-only specifically to catch the relative-import breakage that is this repo's most common failure
- UAT is mine to run, both backend curl and browser. OpenRouter credit spend is authorized; keep runs small (short prompt, trimmed `COUNCIL_MODELS`)

**Default branch is `master`.** Every td-flow command says `origin/main` — substitute throughout.

**Preserved content.** The repo's original root `CLAUDE.md` (167 lines) is verbatim at `.td-flow/frameworks/preserved-claude-md.md`; the operational parts are distilled into `WORKWAY.md § Framework specifics`. Its "Future Enhancement Ideas" list is a reasonable menu when picking the first piece.

**Unverified.** Model IDs in `backend/config.py` are pinned to Nov 2025 (`openai/gpt-5.1`, `google/gemini-3-pro-preview`, `anthropic/claude-sonnet-4.5`, `x-ai/grok-4`). Nothing has confirmed they still resolve on OpenRouter — check the catalog before treating a failed call as a code bug. This is a likely first casualty of the end-to-end test in step 4.
