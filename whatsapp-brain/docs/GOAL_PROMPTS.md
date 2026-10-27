# /goal prompts for building WhatsApp Chat Brain

Paste these one at a time into Claude Code, in auto mode, on branch
`claude/youthful-ptolemy-fkm028`. Each goal finishes on its own when its condition is
met. Start the next milestone only after the previous one reports **achieved** (type
`/goal` to check its status).

Splitting the build into milestones helps on a Pro plan. When a usage limit pauses a
goal, the finished milestones are already committed. A short goal is also easier for
the evaluator to judge.

---

## M1: Foundation, parser, delta, data model

```
/goal Implement milestone M1 of whatsapp-brain/docs/APP_SPEC.md (read the whole spec first; work only inside whatsapp-brain/).
Scope: repo scaffold (backend Python package, frontend Vite React TS app, pyproject, lint/format/type-check config), SQLite models + Alembic migrations, streaming WhatsApp export parser (Android + iOS formats, .txt and .zip, all cases in spec 4.1), URL extraction + normalization + short-link expansion (mockable), dedup into Resources with Mentions, message fingerprints and idempotent delta ingest with IngestRun stats (spec 4.2), synthetic export generator script (>=12,000 URLs, plus an "extended" version), FastAPI app with /health and ingest endpoints, docs/ARCHITECTURE.md and docs/DECISIONS.md started.
Done when, each shown by command output in this conversation:
1. pytest passes (exit 0) with tests for every format/edge case in spec 4.1 and delta cases in 4.2, including: same file twice adds 0; extended file adds exactly the new items.
2. Parsing the 12,000+ URL synthetic file completes and prints counts.
3. ruff + mypy (backend) and tsc + eslint (frontend) are clean.
4. Work committed and pushed to claude/youthful-ptolemy-fkm028; git status clean.
Do not edit APP_SPEC.md. Stop after 40 turns.
```

## M2: Fetching, LLM providers, analysis, categories

```
/goal Implement milestone M2 of whatsapp-brain/docs/APP_SPEC.md (re-read spec sections 4.3, 4.4, 4.5, 4.9; build on M1 code in whatsapp-brain/).
Scope: resumable async fetch job system with per-domain rate limits, cache, retries, pause/resume; extractor plugin registry with GitHub API, generic article (trafilatura), YouTube, and LinkedIn/blocked-site fallback using OpenGraph + WhatsApp context marked partial with a retry queue; provider abstraction with Ollama, OpenAI-compatible, Gemini, Anthropic adapters and a deterministic fake provider; keyring secret storage; structured per-resource analysis with Pydantic validation + retry + content-hash cache; chunking + local fastembed embeddings into LanceDB + SQLite FTS5; cost/token estimate; category auto-discovery (clustering + LLM naming), multi-label classification of new resources, Uncategorized + new-category suggestions; REST endpoints for all of it.
Done when, each shown by command output in this conversation:
1. pytest passes (exit 0), all HTTP mocked with recorded fixtures (respx), no live network; tests cover each extractor, each provider adapter (mocked HTTP), resume-after-crash, invalid-LLM-JSON retry, category discovery and classification with the fake provider.
2. Full pipeline on the 12,000+ URL synthetic file (mock fetch + fake provider) finishes and prints status counts per state and per category.
3. ruff, mypy, tsc, eslint clean.
4. Committed and pushed to claude/youthful-ptolemy-fkm028; git status clean.
Do not edit APP_SPEC.md. Stop after 50 turns.
```

## M3: Federated Obsidian brains and RAG over RAG

```
/goal Implement milestone M3 of whatsapp-brain/docs/APP_SPEC.md (re-read sections 4.6 and 4.7; build on M1-M2 in whatsapp-brain/).
Scope: vault writers for main brain + per-category brains (separate vaults default, single-vault option), resource/topic/entity/hub/index notes with frontmatter and wikilinks, Windows-safe filenames, managed-section markers preserving user edits, vault backup before bulk update, delta append on re-ingest; per-brain indexes and brain summaries; query router choosing brains, hybrid retrieval with RRF, answer synthesis with citations and obsidian:// links, intents incl. date/sender filters, streaming chat API with session memory.
Done when, each shown by command output in this conversation:
1. pytest passes (exit 0) including a vault validation test (all wikilinks resolve, frontmatter parses, filenames Windows-safe), a test that user edits outside markers survive an update, a delta test that an extended ingest adds exactly the new notes, and router/retrieval tests with the fake provider asserting correct brains are selected and citations returned.
2. Building brains for 3 categories from the synthetic dataset prints vault note counts.
3. ruff, mypy, tsc, eslint clean.
4. Committed and pushed to claude/youthful-ptolemy-fkm028; git status clean.
Do not edit APP_SPEC.md. Stop after 40 turns.
```

## M4: UI and local voice

```
/goal Implement milestone M4 of whatsapp-brain/docs/APP_SPEC.md (re-read sections 4.8 and 4.10; build on M1-M3 in whatsapp-brain/).
Scope: all UI screens in 4.10 (Dashboard, Ingest with live progress + delta report, Resources, Categories editor with rename/merge/split/move, Brains, Chat with streaming + clickable citations, Settings for providers/models/vault/fetch/voice, Jobs/Logs); local voice: browser mic capture, push-to-talk + VAD hands-free, faster-whisper STT, Piper or Kokoro TTS with barge-in, model download on first use with progress, separate conversational speech response (not reading the text answer), voice actions via tool-calling with confirmation for long/destructive actions.
Done when, each shown by command output in this conversation:
1. Vitest + tsc + eslint pass; pytest passes (exit 0) including voice pipeline tests with a recorded audio fixture for STT, TTS producing non-empty audio, and speech-style response + voice-action tool-calling tests with the fake provider.
2. Playwright e2e passes (headless Chromium, fake provider): ingest synthetic file -> review categories -> build brain for 2 categories -> ask a text question and see citations.
3. Committed and pushed to claude/youthful-ptolemy-fkm028; git status clean.
Do not edit APP_SPEC.md. Stop after 50 turns.
```

## M5: Windows packaging, CI, docs, hardening

```
/goal Implement milestone M5 of whatsapp-brain/docs/APP_SPEC.md (re-read sections 2, 5 and 6; build on M1-M4 in whatsapp-brain/).
Scope: PyInstaller one-dir build bundling backend + built frontend, launcher that starts the server on 127.0.0.1 and opens the browser, data dir in %LOCALAPPDATA%\WhatsAppBrain, Inno Setup installer with Start-menu/desktop shortcuts and uninstaller; GitHub Actions workflow (.github/workflows/whatsapp-brain.yml) running all tests on ubuntu-latest and windows-latest, building the installer on windows-latest, silently installing it, starting it and passing a /health smoke test, uploading the installer as an artifact; security review (localhost bind, HTML sanitizing, keyring), log rotation; README (install, first run, Ollama, BYOK, Obsidian, voice, troubleshooting), ARCHITECTURE.md, DECISIONS.md, CHANGELOG.md.
Done when ALL quality gates in spec section 6 hold, each shown in this conversation: local pytest/vitest/playwright/lint output, the coverage report showing >=80% on backend core, and the latest GitHub Actions run of the workflow on claude/youthful-ptolemy-fkm028 is green on both OSes with the installer artifact present (show the run status). If a CI run fails, read its logs, fix and push until green. Committed and pushed; git status clean.
Do not edit APP_SPEC.md. Stop after 50 turns.
```

---

## Single-shot alternative (better suited to Max plans)

```
/goal Build the complete app specified in whatsapp-brain/docs/APP_SPEC.md, working through milestones M1-M5 in whatsapp-brain/docs/GOAL_PROMPTS.md in order, committing and pushing to claude/youthful-ptolemy-fkm028 after each milestone. Done when every quality gate in APP_SPEC.md section 6 is shown satisfied in this conversation with command output, and the latest GitHub Actions run of .github/workflows/whatsapp-brain.yml on this branch is green on ubuntu-latest and windows-latest with the Windows installer artifact uploaded. Do not edit APP_SPEC.md. Stop after 230 turns.
```
