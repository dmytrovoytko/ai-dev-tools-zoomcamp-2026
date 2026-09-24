# AGENTS.md — Mini-Kanban App

## Stack
Flask + Jinja + HTMX + SQLite (stdlib `sqlite3`) + PicoCSS via CDN. No build step, no ORM in v1.

## Structure (expected)
```
app.py / app/ (routes, db.py, auth.py)
templates/ (layout.html, boards.html, board.html, partials/column.html, share.html)
static/ (minimal custom css)
_docs/specs.md (source of truth for v1 scope)
```

## Conventions
- Server-rendered HTML, HTMX partials for card/column mutations (`hx-post`, `hx-target`, `hx-swap="outerHTML"`).
- No custom JS except htmx CDN. Keep PicoCSS classes, minimal custom CSS.
- Passwords: werkzeug hashing only. Session cookie auth. Check `board.user_id == current_user.id` on every mutation.
- SQLite: one file (`instance/kanban.db`), foreign keys ON, parameterized queries only.
- Cards: `title` 1–200 chars required; `priority` in `none|low|medium|high`; archive = set `archived_at`, never hard-delete in v1.
- Share tokens: `secrets.token_urlsafe(32)`, 404 on revoked/expired.

## Commands
- Run: `flask --app app run --debug`
- Venv: `python -m venv .venv && source .venv/bin/activate && pip install flask`
- DB init: `flask --app app init-db` (to implement)

## Notes for agents
- Keep scope to `_docs/specs.md` v1. Don't add drag-drop, search, roles.
- Prefer small diffs, server-side validation, return column partials after mutations.
- Short answers, one question at a time when scoping.
