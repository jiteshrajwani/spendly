# Spec: Registration

## Overview
This step implements user registration for Spendly. It wires up the existing `/register` route and `register.html` form to actually create accounts in the `users` table using the data layer built in Step 1. Once complete, a visitor can submit the registration form, get a hashed password stored in SQLite, and be redirected toward login. This is the first piece of real authentication logic in the app — no session/login state is introduced yet, that is a later step.

## Depends on
- Step 1 — Database Setup (`database/db.py` with `get_db()`, `init_db()`, `users` table) — already complete.

## Routes
- `GET /register` — render the registration form — public (already exists, unchanged)
- `POST /register` — validate input, create the user, redirect on success — public (new logic added to the existing `register` view)

## Database changes
No database changes. The `users` table (`id`, `name`, `email`, `password_hash`, `created_at`) already supports registration as-is. Registration will `INSERT` into this existing table using a parameterized query.

## Templates
- **Create:** none
- **Modify:** `templates/register.html` — no structural changes expected; it already posts `name`, `email`, `password` to `/register` and already renders `{{ error }}` when passed. Only touch this file if validation requires a new field-level error display.

## Files to change
- `app.py` — add `POST` handling to the `register` view: read form fields, validate, hash password, insert user, handle duplicate email, redirect to `/login` on success or re-render `register.html` with an `error` on failure.

## Files to create
None.

## New dependencies
No new dependencies. Use `werkzeug.security.generate_password_hash` (already used in `database/db.py`).

## Rules for implementation
- No SQLAlchemy or ORMs
- Parameterised queries only
- Passwords hashed with werkzeug (`generate_password_hash`)
- Use CSS variables — never hardcode hex values
- All templates extend `base.html`
- Validate required fields (`name`, `email`, `password`) are present and non-empty server-side, not just via HTML `required`
- Catch duplicate email (`sqlite3.IntegrityError` from the `UNIQUE` constraint on `email`) and show a friendly error rather than a 500
- No session/login logic yet — successful registration redirects to `/login`, it does not log the user in
- Use `url_for()` for every internal link — never hardcode URLs

## Definition of done
- [ ] Submitting the register form with a new name/email/password creates a row in `users` with a hashed (not plaintext) password
- [ ] After successful registration, the browser is redirected to `/login`
- [ ] Submitting with an email that already exists (e.g. `demo@spendly.com`) re-renders `register.html` with an error message and does not create a duplicate row
- [ ] Submitting with a missing field re-renders `register.html` with an error message and does not hit the database
- [ ] `GET /register` still renders the form as before
- [ ] App starts without errors and existing routes are unaffected
