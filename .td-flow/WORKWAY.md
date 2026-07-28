# Way of work

How this project gets tested, verified, and shipped. Section headers are locked. Add and update values as we learn the project. CLAUDE.md routes natural-language statements to the right section here.

Multi-stack projects (e.g. Python + C++, Laravel + frontend): each section may have multiple subsections — one per stack. Use H3 (`### Python`, `### C++`) for splits. Single-stack projects keep it flat.

## Local testing

Automated checks run before committing. The pre-commit hook reads `Test command` from this section.

- Test command:    `cd frontend && npm run lint && cd .. && uv run python -c 'import backend.main'`
- Dev server:      `./start.sh` (starts backend :8001 and frontend :5173 together)
- Local URL:       http://localhost:5173 (backend API at http://localhost:8001)
- Pre-ship checklist:
  - [ ] test command passes (lint clean + backend imports)
  - [ ] `./start.sh` brings up both servers without errors
  - [ ] a real query completes all 3 stages if the change touches the council pipeline

There are no unit tests. The test command is a cheap gate, not a suite: ESLint catches React/hook breakage, and `import backend.main` catches the relative-import and syntax breakage that is this repo's most common failure (see § Framework specifics).

### When local testing isn't possible

Some projects can't fully exercise their behavior locally. Here, everything runs locally — but every real answer costs OpenRouter credits.

- What can be tested locally: lint, backend import, server boot, CORS, route shapes, `storage.py` JSON round-trip, ranking-parse functions against fixture text
- What requires the live environment: any actual model output — all three stages call OpenRouter and spend credits
- The workaround for development: test parsing/aggregation logic against saved response text instead of re-querying; when a live run is genuinely needed, trim `COUNCIL_MODELS` to one cheap model and use a short prompt

## Local UAT

Manual verification before pushing. For projects where I can't fully exercise the UI on my own, this is what the user does.

- Who runs it: Claude — both the backend (curl against :8001) and the frontend (browser at :5173)
- What to verify: all 3 stages render; stage 1 tabs show each model; stage 2 shows raw evaluation text with the extracted ranking below it and correct de-anonymization; aggregate rankings look sane; stage 3 chairman answer appears; conversation persists in the sidebar after reload
- How: `./start.sh`, then `POST /api/conversations/{id}/message` for API checks and the browser for the UI pass

Credit spend is authorized for UAT — keep runs small (short prompt, trimmed council) unless the change specifically needs the full council.

## Live

Production / deployed environment. How the change reaches users; how I verify it after pushing.

- Live URL:        none — local-only app, never deployed
- Deploy:          none — "shipping" here means merged to `master` and pushed to `origin`
- Smoke after ship: `./start.sh` and one short query through the UI
- Logs:            terminal output from `./start.sh` (uvicorn + vite, both in the foreground)
- Dashboards:      OpenRouter credit + request usage — https://openrouter.ai/activity

## Framework specifics

Per-framework awareness. Notes on what each framework brings, how to use it, and any gotchas. Updated when the user tells me about a framework or when I research one (e.g. via context7).

### FastAPI + uv (backend)

- Dependencies managed by `uv` — `uv sync` to install, `uv run python -m backend.main` to run. No manual venv activation.
- **Always run as `python -m backend.main` from the project root.** Every backend module uses relative imports (`from .config import ...`); running from inside `backend/` breaks them. This is the repo's #1 gotcha and the reason the test command includes an import check.
- Backend binds **port 8001**, not 8000 — deliberate, to avoid a conflict with another app.
- CORS middleware in `main.py` allows `localhost:5173` and `localhost:3000`. Changing the frontend port means updating this list.
- Async throughout: `openrouter.query_models_parallel()` fans out with `asyncio.gather()`.

### React + Vite (frontend)

- Vite dev server on **5173**. `npm run dev` from `frontend/`; `npm run lint` is the only automated check.
- API base URL lives in `frontend/src/api.js` — must match the backend port in `backend/main.py`.
- Every `ReactMarkdown` render must be wrapped in `<div className="markdown-content">`; that class carries the global 12px padding and markdown styles from `index.css`.
- Light-mode theme only. Primary color `#4a90e2`; stage 3 uses a green tint (`#f0fff0`) to mark the conclusion.
- Component map: `App.jsx` (orchestration + conversation state) → `ChatInterface.jsx` (input) → `Stage1/2/3.jsx` (the three stages) + `Sidebar.jsx` (history).

### OpenRouter

- Single upstream for all models: `https://openrouter.ai/api/v1/chat/completions`, key from `OPENROUTER_API_KEY` in `.env`.
- Council and chairman are hardcoded in `backend/config.py` (`COUNCIL_MODELS`, `CHAIRMAN_MODEL`). Chairman may or may not also be a council member.
- Model IDs pinned in the fork are from Nov 2025 (`openai/gpt-5.1`, `google/gemini-3-pro-preview`, `anthropic/claude-sonnet-4.5`, `x-ai/grok-4`) — verify against OpenRouter's current catalog before assuming a failing call is a code bug.
- Graceful degradation is intentional: a failed model returns `None` and the pipeline continues with whoever answered. Only an all-models failure surfaces to the user.

### Council pipeline design (inherited)

- **Stage 2 anonymization is the point of the project.** Models see "Response A/B/C"; the `label_to_model` map stays server-side and de-anonymization happens client-side purely for display. Don't leak model identity into stage 2 prompts.
- Stage 2 prompt demands a strict `FINAL RANKING:` section with a numbered list so `parse_ranking_from_text()` can read it. A fallback regex scrapes any `Response X` patterns in order when a model ignores the format.
- `calculate_aggregate_rankings()` averages rank position across peer evaluations.
- Metadata (`label_to_model`, `aggregate_rankings`) is returned by the API but **not persisted** to `data/conversations/` — it exists only in the response and frontend state. Reloading a conversation loses it.

### Preserved (pre-td-flow)

The repo's original root `CLAUDE.md` (167 lines of architecture notes, design decisions, gotchas, and an enhancement wishlist) is kept verbatim at `.td-flow/frameworks/preserved-claude-md.md`. The operationally relevant parts are distilled into the subsections above; the original also has a "Future Enhancement Ideas" list worth reading before picking new scope.

## Notes

Project-specific gotchas, flaky tests, env-only checks, anything that future-me needs to know. Keep it short — git carries the rest.

- **Default branch is `master`**, not `main`.
- Fork of `karpathy/llm-council`. Upstream is explicitly unmaintained ("vibe code alert" in the README) — no PRs upstream, and don't expect upstream fixes. Diverging freely is the intended use.
- `data/` is gitignored — conversation history is local only and disappears on a fresh clone.
- The original CLAUDE.md references a `test_openrouter.py` connectivity script; it does not exist in this repo.
