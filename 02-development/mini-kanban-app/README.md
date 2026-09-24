# Mini-Kanban App

Simple multi-user kanban built with Python (Flask) + HTMX + SQLite + PicoCSS.

- Private boards per user (username + password login).
- Boards from templates, columns editable later.
- Cards: title required, description / due date / priority optional.
- Move cards with ← → buttons (HTMX, no page reload).
- Archive / restore (no hard delete in v1).
- Share any board read-only by link — no login needed, revokable + optional expiry.

## Quickstart

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
flask --app app run --debug
```

Open http://127.0.0.1:5000

## Docs

See [_docs/specs.md](_docs/specs.md) for v1 scope and data model.

## Project status

v1 scoped, not yet implemented.
