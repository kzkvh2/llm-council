# State

<!-- Where we are right now. Top section is field-shaped for quick scan. Resume note is free-form prose — as long as it needs to be (planning lives here for multi-step work). Past sessions live in git. -->

Project:  LLM Council
Topic:    idle
Phase:    idle
Blocker:  none
Last:     2026-07-28 — td-flow initialized

## Resume note

Fresh td-flow init on an existing fork of `karpathy/llm-council`. The app works as inherited — no active work, active scope is deliberately empty.

Read PROJECT.md for scope and WORKWAY.md for how this project gets tested and shipped. The pre-td-flow root `CLAUDE.md` is preserved at `.td-flow/frameworks/preserved-claude-md.md`; its "Future Enhancement Ideas" section is a reasonable menu when picking the first piece.

Nothing has been run yet in this checkout — `uv sync` and `cd frontend && npm install` haven't been done, so the test command will fail until deps are installed. `.env` with `OPENROUTER_API_KEY` also needs creating before anything can query models.
