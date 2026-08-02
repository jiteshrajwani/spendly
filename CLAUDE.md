# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

"Spendly" (branded name, shown in `base.html`/`landing.html`) — a Flask expense tracker built as a step-by-step teaching/curriculum project. This is NOT a finished app: most routes are placeholders and core pieces are unimplemented stubs. Check comments before assuming functionality exists.

- `app.py` — Flask entry point; all routes defined here directly (no blueprints).
- `database/db.py` — currently a stub (comments only, no code). Intended to hold `get_db()`, `init_db()`, `seed_db()` using raw `sqlite3` (no ORM), with `CREATE TABLE IF NOT EXISTS` for schema.
- `templates/*.html` — Jinja2 templates, `base.html` is the shared layout extended by the rest.
- `static/css/`, `static/js/main.js` — vanilla CSS/JS, no build tooling, no npm/Node involved anywhere in this repo.

Routes like `/login`, `/register`, `/logout`, `/profile` currently render templates or return literal placeholder strings (e.g. `"Logout — coming in Step 3"`) — there is no session/auth logic yet. Don't assume expense CRUD, auth, or DB access work until you've checked the actual code.

## Running the app

```
pip install -r requirements.txt
python app.py
```

Runs the Flask dev server on **port 5001** (not the Flask default 5000), with `debug=True`.

## Testing

`pytest` and `pytest-flask` are declared in `requirements.txt` but no test files exist yet — running `pytest` will report no tests collected.

## Conventions

- Follow the existing "Step N" comment style used in stub/placeholder code (see `database/db.py`, `app.py`) when scaffolding new pieces — this repo is built incrementally through numbered steps, so don't jump ahead and fully implement future steps unless asked.
- No linter/formatter is configured; match the existing code style already present in the file you're editing.
- `templates/base.html` loads both `style.css` and `landing.css` on every page, not just the landing page — keep this in mind when adding page-specific styles.
