# WhatsApp Chat Brain — Application Specification

Version 1.0 — source of truth for the `/goal` build. Do not weaken requirements in this
file to make a goal pass; if something is truly infeasible, document why in
`docs/DECISIONS.md` and implement the closest workable alternative.

## 1. Purpose

A local-first Windows app that ingests a (very large, 10,000+ URL) WhatsApp chat
export, reads and understands every shared link (LinkedIn, GitHub, blogs, tools…),
classifies them into categories, and builds **federated Obsidian brains**: one main
brain plus one brain per selected category. Users query the brains by **text or local
voice** through a RAG-over-RAG engine. Re-ingesting an updated export processes only the
**delta** and appends it to the right brains. The app keeps evolving, so the code is
modular and extensible.

## 2. Users & platform

- Single user, Windows 10/11 laptop (x64). Must also run on Linux for development/CI.
- Delivered as a Windows **installer (.exe)** that installs a local web app: a Python
  backend + browser UI, launched from a Start-menu/desktop shortcut that starts the
  server and opens the browser at `http://127.0.0.1:<port>`. Optional tray icon.
- Works fully offline when using local models (Ollama / vLLM / LM Studio).

## 3. Tech stack (defaults; change only with a recorded reason in DECISIONS.md)

| Concern | Choice |
|---|---|
| Backend | Python 3.11+, FastAPI, Uvicorn, Pydantic v2 |
| Frontend | React + TypeScript + Vite, built to static files served by FastAPI |
| Metadata DB | SQLite (SQLAlchemy/SQLModel + Alembic migrations) |
| Vector store | LanceDB (embedded, Windows-friendly), plus SQLite FTS5 for keyword/BM25 |
| Embeddings | Local by default via `fastembed` (ONNX, CPU); provider embeddings optional |
| LLM access | LiteLLM-style provider abstraction (own interface, adapters) |
| Fetching | `httpx` (async), `trafilatura` for article text, GitHub REST API, OpenGraph fallback |
| Voice | `faster-whisper` (STT), Silero VAD, Piper or Kokoro-ONNX (TTS) — all local |
| Secrets | `keyring` (Windows Credential Manager) — never plain-text keys on disk |
| Packaging | PyInstaller (one-dir) + Inno Setup installer, built on GitHub Actions `windows-latest` |
| Tests | pytest (+ pytest-asyncio, respx for HTTP fixtures), Vitest, Playwright e2e |

## 4. Functional requirements

### 4.1 Ingestion & parsing
- Upload/select a WhatsApp `.txt` export (and `.zip` export containing it). Streams the
  file; must handle 100 MB+ without loading everything in memory.
- Parse standard Android and iOS export formats: multiple date formats (dd/mm/yy,
  mm/dd/yy, yyyy-mm-dd, with/without seconds, 12h/24h, `[date, time]` iOS brackets),
  multi-line messages, system messages, `<Media omitted>`, unicode/RTL, emoji, BOM.
  Auto-detect the format; allow user override of day/month order.
- For each message keep: timestamp, sender, full text, URLs found. Extract every URL
  (including ones without scheme like `github.com/x/y`).
- **URL normalization**: lowercase host, strip tracking params (`utm_*`, `si`, `fbclid`,
  `trk`, `lipi`, …), remove fragments where safe, expand short links (`lnkd.in`, `bit.ly`,
  `t.co`) with a bounded HEAD/GET, canonicalize GitHub URLs to `owner/repo`.
- **Deduplication**: one Resource per canonical URL; record every mention (message,
  sender, date, surrounding text) so context accumulates.

### 4.2 Delta detection (re-ingest)
- Each message gets a stable fingerprint (normalized timestamp + sender + text hash).
- Re-ingesting an updated export inserts only unseen messages/URLs; already-processed
  resources get new mentions appended but are not re-fetched unless stale (configurable)
  or previously failed.
- Every ingest is an **IngestRun** with stats (new messages, new URLs, duplicates,
  failures) shown in the UI. Ingest is idempotent: running the same file twice adds
  nothing.

### 4.3 Fetching & extraction (best effort + fallback)
- Async worker pool with per-domain rate limiting, global concurrency cap, timeouts,
  retries with backoff, robots-respecting user agent, on-disk response cache.
- Extractors (plugin registry, easy to add more):
  - **GitHub**: REST API (optional user token) → description, topics, stars, language,
    README (truncated), latest release.
  - **Generic web/article**: trafilatura main text + title + OpenGraph/meta.
  - **YouTube**: title/description/transcript when available.
  - **LinkedIn & other blocked sites**: no login scraping. Use OpenGraph/preview if
    reachable, else the URL slug + the WhatsApp message text around it. Mark as
    `partial` and put into a retry queue.
- Status per resource: `pending | fetched | partial | failed | skipped`, with error.
- Job system is **resumable**: closing the app mid-run and restarting continues where it
  stopped. Progress bar with ETA, pause/resume/cancel.

### 4.4 Analysis
- For each resource, LLM produces structured JSON (validated with Pydantic, retried on
  invalid output): title, 2–4 sentence summary, key points, entities (people, orgs,
  tools, technologies), content type (repo, article, post, tool, video, paper…),
  tags, quality/relevance score.
- Text is chunked (semantic/heading-aware, ~500–800 tokens) and embedded.
- Cost control: batch requests, cache LLM results by content hash, configurable model
  per task (cheap model for classification, stronger for answers), token/cost estimate
  shown before large runs and a running tally after.

### 4.5 Categories (auto-discover, then user edits)
- First ingest: cluster resource embeddings (e.g. HDBSCAN/k-means), LLM names and
  describes each cluster → proposed taxonomy (target 8–25 categories, can be nested one
  level).
- UI to rename, merge, split, delete, create categories and move resources; multi-label
  assignment allowed (a resource can belong to several categories).
- Later ingests classify new resources into the existing taxonomy; resources that fit
  nothing well go to `Uncategorized` and the app **suggests new categories** when
  enough similar ones accumulate. User edits are never silently undone.

### 4.6 Federated Obsidian brains
- User selects one or many categories → app builds/updates:
  - **Main brain** vault: `Home.md` index, one hub note per category (description,
    stats, top resources, links into the category brain), cross-category topic/entity
    notes, and links between related categories.
  - **Category brain** vault per selected category: one note per resource and topic /
    entity notes (people, tools, technologies) with backlinks; category `Index.md` (Map
    of Content).
- Resource note: YAML frontmatter (`url`, `canonical_url`, `type`, `categories`, `tags`,
  `first_shared`, `shared_by`, `mentions`, `status`, `id`), summary, key points,
  extracted excerpts, WhatsApp context quotes, `[[wikilinks]]` to related notes.
- Layout configurable: separate vault folders (default) or one vault with sub-folders.
- **Delta append**: new resources become new notes; existing notes get new mentions
  added. App-generated content lives between `<!-- brain:start -->` / `<!-- brain:end -->`
  markers so **user edits outside the markers are preserved**. Never delete user notes.
- Valid Obsidian output: safe filenames on Windows (reserved names, length, illegal
  characters), unique slugs, working wikilinks, `.obsidian` config with sensible
  defaults (graph colours per category).

### 4.7 Query engine — RAG over RAG
- Each brain has its own index (vector + FTS) plus a **brain summary** (category
  description + representative content).
- Query flow: (1) router uses the main brain to pick relevant category brains (or uses
  the user's selection), (2) hybrid retrieval (vector + BM25, reciprocal rank fusion,
  optional reranker) within each selected brain, (3) aggregate, (4) LLM synthesizes an
  answer with **citations** (resource notes + original URLs, clickable; open in Obsidian
  via `obsidian://` URI).
- Intent handling: question answering, "find me tools for X", comparisons, "what was
  shared about X last month" (date/sender filters), summarize a category.
- Conversation memory within a chat session; streaming responses.

### 4.8 Voice (local, conversational)
- Push-to-talk and hands-free (VAD) modes in the chat screen; microphone via browser.
- STT: faster-whisper locally (model size selectable; downloads on first use with
  progress; works offline after).
- TTS: Piper or Kokoro locally; selectable voice and speed; barge-in (user can interrupt).
- **Conversational style**: voice mode does not read out the text answer. A separate
  "speech" response is generated: short, natural, human-like ("I found 12 links on
  agent frameworks — the standouts are… want me to open them or build a brain?"), while
  the full text answer with citations still appears on screen.
- **Voice actions** via LLM tool-calling: search/ask, ingest file, build/update brains
  for named categories, open a note in Obsidian, show category stats, rename category.
  Destructive or long-running actions require spoken or click confirmation.
- Voice works with local LLMs (no cloud needed).

### 4.9 LLM providers (BYOK & local)
- Day one: **Ollama**, **OpenAI-compatible** endpoint (vLLM, LM Studio, OpenRouter,
  OpenAI, etc.), **Google Gemini**. Also **Anthropic** and generic adapter interface so
  more providers are a small addition.
- Settings UI: add multiple providers, test connection, list models, choose model per
  role (classification, summarization, answering, voice-speech, embeddings).
- Keys stored in OS keyring. Graceful errors for rate limits / timeouts / bad keys.
- A deterministic **fake provider** exists for tests and offline demo.

### 4.10 UI screens
Dashboard (stats, last ingest), Ingest (upload, progress, delta report), Resources
(table with search/filter/status, retry failed), Categories (taxonomy editor),
Brains (select categories, build/update, open vault), Chat (text + voice), Settings
(providers, models, vault path, fetch limits, voice), Logs/Jobs.

## 5. Non-functional requirements
- **Scale**: 10,000+ URLs ingest and analysis complete without crashing; UI stays
  responsive; memory under ~2 GB excluding local models.
- **Privacy**: all data local in `%LOCALAPPDATA%\WhatsAppBrain` (configurable); only the
  user-chosen LLM provider receives content; no telemetry.
- **Reliability**: resumable jobs, DB migrations, backups of vault before bulk updates,
  structured logs with rotation.
- **Extensibility**: plugin registries for extractors, providers, vault writers.
- **Security**: server binds to 127.0.0.1 only; CSRF-safe; sanitize fetched HTML; no
  arbitrary code execution from content.
- **Docs**: README (install, first run, Ollama setup, BYOK setup, Obsidian usage, voice),
  `docs/ARCHITECTURE.md`, `docs/DECISIONS.md`, CHANGELOG.

## 6. Quality gates (definition of done)
1. `pytest` passes with ≥ 80 % line coverage on backend core (parser, normalizer, delta,
   classifier pipeline, vault writer, RAG router), using the fake provider and recorded
   HTTP fixtures — no network needed.
2. Frontend: type-check, lint and Vitest pass; Playwright e2e covers ingest → categories
   → build brain → text chat with citations (fake provider).
3. Scale test: a generated synthetic WhatsApp export with ≥ 12,000 URLs (fixture
   generator in repo) runs through parse + delta + mock-fetch + fake-analysis + vault
   build; second ingest of an extended file adds exactly the new items.
4. Vault validation test: all wikilinks resolve, frontmatter parses, filenames are
   Windows-safe, user edits outside markers survive an update.
5. Voice: unit tests for the voice pipeline with recorded audio fixture (STT) and TTS
   producing audio; speech-style response generation tested with fake provider.
6. GitHub Actions workflow on `windows-latest` runs backend + frontend tests, builds
   the PyInstaller app and the Inno Setup installer, silently installs it, starts it,
   and passes an HTTP `/health` smoke test; installer uploaded as a workflow artifact.
   Same tests also run on `ubuntu-latest`.
7. README and docs complete; `git status` clean; all pushed.
