# Backlog — Household Chores Manager (Django)

Based on `household-chores-plan.md` v1 spec:
1. Chores with custom repeat, 2. Kanban board, 3. Manual assign + overdue highlight.

## Task 1: Scaffold Django project + chores app
- Install Django with `uv`, `django-admin startproject config .`
- `python manage.py startapp chores`, add `'chores'` to `INSTALLED_APPS` in `config/settings.py`
- Wire `config/urls.py`, run migrations, verify `runserver`
- Acceptance: `uv run python manage.py check` passes, home page loads

## Task 2: Chore model with custom repeat
- Fields: `title`, `area` (house/cat/garden), `assignee` (char), `interval_days` (int), `due_date` (date), `status` (todo/today/this_week/doing/done), `last_done`
- `is_overdue()` helper, `__str__`
- Migration
- Acceptance: create chores in shell, overdue logic correct

## Task 3: CRUD + admin
- Register `Chore` in admin
- List / create / edit / delete views (generic CBVs), base template
- Acceptance: manage chores without shell

## Task 4: Kanban board view
- Single board page grouping by status: To do / Today / This week / Doing / Done
- Move actions (buttons to advance status), filter by area
- Acceptance: chores appear in right column, status change persists

## Task 5: Manual assign + overdue highlight
- Assignee dropdown/text on form, assign on board
- Overdue CSS highlight (due_date < today and status != done), overdue section on top
- Acceptance: overdue chores red/highlighted, assign works

## Task 6: Tests
- Model tests: overdue, default status
- View tests: board grouping, status transition, assign
- Acceptance: `uv run python manage.py test` green
