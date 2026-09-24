# Mini-Kanban App — v1 Specification

## 1. Overview
Simple multi-user kanban with Python + HTMX. Each user owns private boards. Any board can be shared read-only via link (no login required to view).

## 2. Goals (v1)
- Register / login with username + password (server sessions).
- Create boards from pre-defined templates.
- Manage columns (rename / add / delete later — templates first).
- CRUD cards with HTMX partial updates, move via ← → buttons.
- Archive cards (no hard delete in v1).
- Share board read-only by link: revokable + optional expiry.

## 3. Non-goals (v1)
- No drag-and-drop (buttons only).
- No team editing / comments / attachments.
- No search/filter (deferred).
- No email auth, OAuth, roles/permissions beyond owner vs anonymous viewer.

## 4. Users & Auth
- `POST /register`: username (unique, 3-32 chars) + password (min 8). Hash with werkzeug.
- `POST /login`, `POST /logout`. Flask session cookie.
- All `/boards/*` require login except `/s/:token`.
- Owner-only: all mutations check `board.user_id == current_user.id`.

## 5. Boards
- Fields: `id, user_id, title, created_at`.
- Actions: create (pick template), rename, delete (cascades columns/cards/shares).
- Templates (pre-defined, v1):
  - `Basic`: To Do / Doing / Done
  - `Bug Triage`: Backlog / Repro / Fixing / Verifying / Done
  - `Weekly`: Monday..Friday + Done (or Backlog / This Week / Done — pick one, keep 3 templates max)
- Editable later: user can rename/add/delete/reorder columns after creation.

## 6. Columns
- Fields: `id, board_id, title, position`.
- Rules:
  - Position 0..n, unique per board.
  - Delete column: must archive or move its cards first (v1: block if non-empty, show message).
  - Rename inline via HTMX.

## 7. Cards
- Fields: `id, column_id, board_id (denormalized for queries), title*, description?, due_date?, priority?, archived_at?, position, created_at`.
- Validation:
  - `title` required, 1-200 chars.
  - `description` optional, markdown-as-text (no render in v1).
  - `due_date` optional, `YYYY-MM-DD`, no past-date block (just highlight overdue).
  - `priority`: `none | low | medium | high`, default `none`.
- Actions (all HTMX, return column partials):
  - Create in column, edit inline/modal, move left/right (swap column, append to end), archive / unarchive.
- Archive: `archived_at IS NOT NULL` hides from board, visible in `/boards/:id/archive` with restore.

## 8. Sharing (read-only)
- Fields: `id, board_id, token (urlsafe 32), expires_at?, revoked_at?, created_at`.
- `POST /boards/:id/share` → creates/lists link `/s/:token`.
- `POST /boards/:id/share/revoke` sets `revoked_at`.
- Optional `expires_at` datetime; expired or revoked → 404.
- `/s/:token`: public, no login, renders board + columns + non-archived cards only. No buttons/forms, PicoCSS read-only.
- Owner can regenerate token (revoke old + create new).

## 9. UI / UX
- Stack: Flask + Jinja + HTMX + PicoCSS (CDN). No build step.
- Layout:
  - `/`: landing → redirect to `/boards` if logged in.
  - `/boards`: grid of boards + new-board-from-template form.
  - `/boards/:id`: columns side-by-side (horizontal scroll), each card: title, priority badge, due date, ← →, edit, archive.
  - `/boards/:id/archive`: list with restore.
- HTMX: `hx-post` for move/archive/create, `hx-target` column div, `hx-swap="outerHTML"`.
- No custom JS in v1 except htmx CDN script.

## 10. Data Model (SQLite)
```sql
users(id INTEGER PK, username TEXT UNIQUE, password_hash TEXT, created_at DATETIME);
boards(id INTEGER PK, user_id FK, title TEXT, created_at DATETIME);
columns(id INTEGER PK, board_id FK, title TEXT, position INT);
cards(id INTEGER PK, board_id FK, column_id FK, title TEXT, description TEXT,
      due_date DATE, priority TEXT DEFAULT 'none', position INT,
      archived_at DATETIME, created_at DATETIME);
shares(id INTEGER PK, board_id FK, token TEXT UNIQUE, expires_at DATETIME,
       revoked_at DATETIME, created_at DATETIME);
```

## 11. Routes (sketch)
```
GET  /, /register, /login, /boards, /boards/:id, /boards/:id/archive, /s/:token
POST /register, /login, /logout
POST /boards (title + template)
POST /boards/:id/rename, /boards/:id/delete
POST /boards/:id/columns (title), /columns/:id/rename, /columns/:id/delete
POST /boards/:id/cards (column_id + fields)
POST /cards/:id/edit, /cards/:id/move (direction), /cards/:id/archive, /cards/:id/restore
POST /boards/:id/share, /boards/:id/share/revoke
```

## 12. Acceptance Criteria
- [ ] Register/login/logout works, passwords hashed, private boards isolated.
- [ ] Create board from each template yields correct columns.
- [ ] Card CRUD + ← → move works without full page reload.
- [ ] Archive hides card, archive page restores it.
- [ ] Share link views board without login; revoke/expire → 404.
- [ ] Column add/rename/delete (block non-empty delete) works.

## 13. Future (post-v1)
Custom templates, drag-and-drop, search/filter, due reminders, markdown render, team edit roles.
