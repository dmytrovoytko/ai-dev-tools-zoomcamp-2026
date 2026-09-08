# Dev Setup — Household Chores Django Project

## 1. Prerequisites
- Python 3.12+
- `uv` recommended: https://docs.astral.sh/uv/

```bash
python3 --version
uv --version
```

## 2. Install Django
```bash
# in your homework repo root
uv init --bare 2>/dev/null || true
uv add django
# or without uv:
# pip install django
```

Verify:
```bash
uv run python -m django --version
```

## 3. Create Django project
```bash
uv run django-admin startproject config .
# creates: manage.py, config/settings.py, config/urls.py
```

Run dev server to verify:
```bash
uv run python manage.py migrate
uv run python manage.py runserver
# open http://127.0.0.1:8000/
```

## 4. Create chores app
```bash
uv run python manage.py startapp chores
# creates: chores/models.py, chores/views.py, etc.
```

## 5. Register app in project
Edit `config/settings.py`, add `'chores'` to `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'chores',  # <-- add this
]
```

This answers Homework Q3: file to edit is `settings.py`.

## 6. Verify app wiring
```bash
uv run python manage.py check
uv run python manage.py makemigrations chores
uv run python manage.py migrate
uv run python manage.py runserver
```
