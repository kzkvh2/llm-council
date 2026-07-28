# LLM Council

## What this is

A local web app that sends one question to several LLMs via OpenRouter, has them anonymously review and rank each other's answers, then has a designated "chairman" model synthesize the final response. Fork of `karpathy/llm-council`.

## Who it's for

Me — personal, local, single-user. A way to see multiple frontier models answer the same question side by side and see what they think of each other's answers.

## Stack & choices

<!-- Current picks only. No history, no reasoning. Edit freely when you change your mind. -->

- Language/runtime: Python ≥3.10 (backend), Node/JS (frontend)
- Framework: FastAPI + uvicorn (backend), React 19 + Vite 7 (frontend)
- Hosting/deploy: none — runs locally only (backend :8001, frontend :5173)
- Database: none — JSON files in `data/conversations/` (gitignored)
- Auth: none — local single-user; the only secret is `OPENROUTER_API_KEY`
- Key libs: httpx (async), pydantic, python-dotenv, react-markdown
- Model access: OpenRouter for every model call, council + chairman set in `backend/config.py`

## Active scope

<!-- What we're building toward right now. Bullets. Move to "Shipped" when live. -->

(nothing yet — rails set up, first piece not chosen)

## Shipped

<!-- What's live. No dates, no commit hashes — git has those. -->

- 3-stage council pipeline: parallel first opinions → anonymized peer ranking → chairman synthesis (inherited from upstream, working)
- Frontend tab views for stage 1 and stage 2 with client-side de-anonymization and parsed-ranking validation
- JSON conversation storage + sidebar history

## Out of scope

<!-- Explicit "no" list. One-line reason if non-obvious. -->

- Upstream contributions — author explicitly does not maintain or accept improvements to `karpathy/llm-council`
- Public deployment — no auth, and the OpenRouter key is a plain env var, so anything exposed spends real credits
- Multi-user / accounts — storage is flat JSON on local disk by design
