# Aral Study Tool

*Aral* is Filipino for "study". Paste your notes and get a **summary**, **flashcards**, a **practice quiz**, or a list of **key terms**.

**Live demo:** https://romar-zaratan.github.io/ai-study-tool/

## Features

- Paste notes, drop a `.txt` / `.md` file, or try the built-in sample notes (object detection basics)
- Four outputs: summary, flip-card flashcards (keyboard controls, shuffle, know / still learning), multiple-choice quiz with instant feedback and a score, key terms
- Export as Markdown (copy or download) or print
- Recent results saved in the browser
- Input checks (too short, too long, wrong file type), loading steps, cancel, timeouts, clear error messages
- Light and dark mode, keyboard accessible, works on phones

## Two modes

| Mode | When | How results are made |
|---|---|---|
| **Demo** | `API_URL` is empty (default) | An offline, extractive method in the browser: ranks sentences by word frequency, finds "X is …" definitions, builds quiz choices from other terms. Labeled clearly as **not AI**. |
| **AI** | `API_URL` points to a backend | The backend sends the notes to an LLM and returns structured JSON. |

## Why a backend is needed for AI mode

An AI API key must **never** be in browser code: anyone can open DevTools and copy it. So the page only talks to *your* small backend, and the backend holds the key in an environment variable.

## Backend contract

`POST {API_URL}` with JSON:

```json
{ "notes": "…", "type": "summary | flashcards | quiz | terms", "count": 5, "lang": "en | fil" }
```

Return JSON matching the type:

```json
{ "type": "summary",    "title": "…", "overview": "…", "points": ["…"] }
{ "type": "flashcards", "title": "…", "cards": [{ "front": "…", "back": "…" }] }
{ "type": "quiz",       "title": "…", "questions": [{ "q": "…", "choices": ["…","…","…","…"], "answer": 0, "explain": "…" }] }
{ "type": "terms",      "title": "…", "terms": [{ "term": "…", "def": "…" }] }
```

Errors: any 4xx/5xx with `{ "error": "message for the user" }`. Status 429 shows a "wait a minute" message.

The page validates every response before showing it, so a malformed answer shows a friendly error instead of breaking.

### Backend checklist (planned)

- One endpoint on Railway (Laravel or a small Node server)
- API key in an environment variable, never in the repo
- CORS limited to the GitHub Pages origin
- Limit note length (12,000 characters) and rate-limit per IP
- Ask the model for JSON in the exact shapes above, and validate before returning

## Tech

Plain HTML, CSS and JavaScript in one file · no framework, no dependencies

## Built by

John Romar Z. Francisco · [Portfolio](https://romar-zaratan.github.io/)
