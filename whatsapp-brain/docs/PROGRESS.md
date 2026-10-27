# Progress

## Current session
- Session: (not started)
- Start (UTC):

## Milestones and tasks
Tick `[x]` only when the task's tests pass and it is committed. Each milestone closes
with an Opus acceptance check (BUILD_PROTOCOL §5).

### M0 Plan (Opus)
- [ ] Opus: architecture and module plan → `docs/ARCHITECTURE.md`; break M1–M5 below into tasks of about 15–30 minutes each (edit this file)
- [ ] Opus: `docs/DESIGN_SYSTEM.md` (tokens, typography, components, screen wireframes in text) per spec 4.11

### M1 Foundation, parser, delta (spec 4.1, 4.2)
- [ ] Repo scaffold: backend package, Vite + React + TS + Tailwind + shadcn/ui frontend, lint/format/type-check, CI-ready scripts
- [ ] SQLite models + Alembic migrations
- [ ] Streaming WhatsApp parser (Android/iOS, .txt/.zip, all spec edge cases)
- [ ] URL extraction, normalization, short-link expansion (mockable), dedup and mentions
- [ ] Message fingerprints, idempotent delta ingest, IngestRun stats
- [ ] Synthetic export generator (≥12,000 URLs plus an "extended" file)
- [ ] FastAPI app with /health and ingest endpoints
Done when: pytest passes, including all 4.1/4.2 cases (same file twice adds 0; the extended file adds exactly the new items); the 12k file parses; ruff, mypy, tsc and eslint are clean.

### M2 Fetching, providers, analysis, categories (spec 4.3, 4.4, 4.5, 4.9)
- [ ] Resumable async fetch jobs (rate limits, cache, retries, pause/resume)
- [ ] Extractors: GitHub API, article, YouTube, LinkedIn/blocked fallback with retry queue
- [ ] Provider layer: Ollama, OpenAI-compatible, Gemini, Anthropic, and a fake provider; keyring storage
- [ ] Structured analysis with validation, retry and cache; chunking; fastembed → LanceDB + FTS5
- [ ] Cost/token estimates
- [ ] Category discovery (clustering + LLM naming), multi-label classification, suggestions
Done when: pytest passes with all HTTP mocked; the full pipeline on the 12k file (mock fetch + fake provider) prints counts per status and per category; lint and type checks are clean.

### M3 Brains and RAG over RAG (spec 4.6, 4.7)
- [ ] Vault writers (main + category brains), notes, frontmatter, wikilinks, Windows-safe names
- [ ] Managed-section markers, vault backup, delta append
- [ ] Per-brain indexes and summaries; router; hybrid retrieval with RRF
- [ ] Answer synthesis with citations and obsidian:// links; intents and filters; streaming chat API
Done when: pytest passes, including vault validation, preservation of user edits, delta append, and router/citation tests; building 3 brains from the synthetic data prints note counts.

### M4 UI and voice (spec 4.8, 4.10, 4.11)
- [ ] App shell: sidebar, command palette, theming, toasts, empty/loading/error states
- [ ] Dashboard + Ingest screens
- [ ] Resources + Categories screens
- [ ] Brains screen with knowledge-graph view
- [ ] Chat screen (streaming, citation chips, follow-ups, history)
- [ ] Settings + Jobs/Logs screens
- [ ] Local voice: STT, VAD, TTS, barge-in, model download, speech-style replies, voice actions with confirmation, mic orb UI
- [ ] Opus UI review → `docs/UI_REVIEW.md`; fix all findings
Done when: Vitest, tsc and eslint pass; pytest voice tests pass; Playwright e2e (ingest → categories → brain → chat with citations) passes; light and dark screenshots of every screen are committed; axe shows no serious or critical issues.

### M5 Windows packaging, CI, docs (spec 2, 5, 6)
- [ ] PyInstaller build + launcher + Inno Setup installer
- [ ] GitHub Actions: tests on ubuntu + windows, build the installer, silent install, /health smoke test, upload the artifact
- [ ] Security hardening, log rotation
- [ ] README, ARCHITECTURE, DECISIONS, CHANGELOG
- [ ] Final Opus acceptance against spec section 6
Done when: every spec §6 gate holds and the latest CI run is green on both OSes with the installer artifact uploaded.

## Session log
(Newest last.)
